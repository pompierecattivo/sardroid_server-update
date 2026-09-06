# Sardroid Server — Changelog

## 9.1.2 - 2026-09-06

Diagnostica del proxy allineata agli scenari reali: il test provava un host fisso e mai la porta 443, quindi non rilevava l'unica strada percorribile nelle reti aziendali restrittive.

### Test proxy: ora prova il broker configurato e la porta 443
- **Bug**: il test in Impostazioni → Proxy provava sempre `test.mosquitto.org` **hardcoded**, e mai la porta **443**. Due conseguenze:
  - le policy dei proxy aziendali sono **per destinazione**: mosquitto poteva risultare raggiungibile mentre il broker realmente in uso era bloccato, dando un esito rassicurante ma falso;
  - le porte provate (1883, 8883, 8080, 8081, 8083, 8084, 8000) sono esattamente quelle che un proxy restrittivo blocca, mentre la 443 — l'unica ammessa — non veniva mai testata. Il risultato era "nessun metodo disponibile" anche quando una strada percorribile esisteva.
- **Fix** in [server.py::test_proxy()](server.py):
  - il bersaglio e' ora `mqtt.external.host`, cioe' il broker davvero configurato (`test.mosquitto.org` resta solo come ripiego se il campo e' vuoto); l'host provato viene riportato nel risultato;
  - la **443** e' in cima alla lista delle porte testate;
  - se l'utente ha configurato una porta non standard (es. 1234), viene provata per prima.
- Aggiunto l'esito **`silent`**: un proxy che accetta la connessione TCP e poi tace sta quasi sempre scartando una richiesta non autenticata. Prima produceva un timeout generico che faceva sembrare il proxy irraggiungibile, nascondendo la causa vera.
- Verificato sulla rete VVF: `mqtt.flespi.io:443` → tunnel aperto; 1883, 8000, 8081, 8083, 8084 e 8883 → `403 Forbidden`.


## 9.1.1 - 2026-09-06

Supporto completo ai broker MQTT raggiungibili solo via WebSocket dietro proxy aziendale, verificato sul campo con dispositivo reale.

### Transport del broker dichiarato nel QR di pairing (campi "tr" e "wp")
- **Problema**: il campo `m` del QR porta solo `host:porta:tls` e **non puo' esprimere il protocollo**. L'app Android assumeva quindi sempre MQTT nativo e costruiva `ssl://host:porta`: contro un broker che sulla stessa porta espone WebSocket (flespi:443) il TLS riusciva ma il CONNECT restava senza risposta, e il pairing falliva in timeout senza un errore comprensibile. La porta da sola non basta a dedurre il protocollo — la 443 puo' essere MQTT nativo su un broker e WebSocket su un altro, con path diversi.
- **Fix lato server** in [dh_service.py::generate_qr_data()](dh_service.py): la parte 1 del QR porta ora due campi **opzionali**
  - `tr` — transport: `tcp` (MQTT nativo), `ws`, `wss`
  - `wp` — path del WebSocket (solo per ws/wss, es. `/mqtt`)
- Il campo `m` resta a tre elementi: i QR restano leggibili dalle app che non conoscono i campi nuovi, e un QR privo di `tr` viene interpretato come `tcp` (comportamento storico).
- Il transport annunciato viene dedotto da [server.py::create_pairing_session()](server.py): eredita quello effettivamente usato dal server, oppure `tcp` se e' configurata una porta dedicata ai dispositivi.
- Aggiunto il setting `mqtt.device.port` (campo "Porta per i dispositivi (QR)" in Impostazioni → Rete): permette di annunciare ai telefoni una porta diversa da quella usata dal server. Utile quando il server esce in WebSocket dietro proxy sulla 443 ma i dispositivi, con rete dati, possono usare la porta MQTT nativa del broker. Vuoto = stessa porta del server.
- **Fix lato app** (progetto `D:\sardroid`, tre file): `SettingsService.kt` conserva `mqttTransport`/`mqttWsPath`; `MqttService.kt` costruisce `wss://host:porta/path` quando richiesto (Paho 1.2.5 supporta nativamente `ws`/`wss`, nessuna nuova dipendenza) e forza il socket factory TLS con `wss`; `PairingScreen.kt` legge `tr`/`wp` dal QR e li applica nei tre punti di configurazione del broker.
- **Verificato end-to-end** con telefono reale in rete dati e server dietro la VPN aziendale: `Broker: wss://mqtt.flespi.io:443/mqtt`, pairing completato, e **442 posizioni di una traccia trasferite con ACK**. La catena `telefono (4G) → flespi ← proxy aziendale ← server (VPN)` funziona.

### Fix: la scelta del metodo di connessione MQTT non veniva salvata
- **Bug**: in Impostazioni → Rete la tendina "Metodo di connessione" e il campo "Porta WebSocket" tornavano sempre ad *Auto-detection* dopo il salvataggio. Causa: `mqtt.connection.method` e `mqtt.connection.ws_port` erano presenti nel form ma **non registrati in `settingsConfig`** ([settings.html](static/settings.html)); il salvataggio itera su quell'elenco, quindi i due campi venivano ignorati in silenzio.
- Effetto pratico: impossibile forzare il metodo WebSocket. L'auto-detection sceglieva `http_connect` (tunnel con MQTT puro), il broker non riconosceva il protocollo e la connessione cadeva in *Keep alive timeout*.
- **Fix**: entrambe le chiavi aggiunte a `settingsConfig`.

### Fix: i metodi 'wss', 'tcp' e 'tls' non erano riconosciuti dal backend
- **Bug**: la tendina offre otto metodi, ma [mqtt_handler.py::connect_client()](mqtt_handler.py) ne riconosceva solo cinque. Selezionando **WebSocket TLS (wss://)** — l'unica scelta corretta per un broker su 443 — il valore finiva nel ramo "metodo non riconosciuto" e ricadeva sull'auto-detection, vanificando la scelta dell'utente. Stessa sorte per `tcp` e `tls`.
- **Fix**: `wss` viene trattato come WebSocket con TLS forzato; `tcp` e `tls` come connessioni dirette, la seconda con TLS.

### Note operative emerse dalla verifica sul campo
- La riserva sollevata nella segnalazione originale (§6: *"verificare che paho instradi il transport WebSocket attraverso il proxy CONNECT"*) e' **risolta**: `proxy_set(socks.HTTP)` instrada correttamente anche il transport `websockets`. Il fallback con socket pre-aperto a mano non serve.
- **I proxy aziendali richiedono autenticazione Basic** (rispondono `407`), dettaglio assente dalla segnalazione originale. Senza credenziali il proxy **scarta la richiesta in silenzio**: si ottiene un timeout che ne maschera la causa e fa sembrare il proxy irraggiungibile. Se la connessione va in timeout, verificare le credenziali prima di concludere che il proxy sia giu'.
- **Porte ammesse dal proxy**: verificato che il `CONNECT` e' consentito **solo verso la 443**; 8081, 8084 e 8884 rispondono `403 Forbidden`. Questo esclude i broker MQTT pubblici anonimi (test.mosquitto.org, broker.emqx.io, broker.hivemq.com), che espongono wss proprio su quelle porte. In una rete cosi' vincolata servono un broker con endpoint wss su 443 (es. flespi, che richiede un token) oppure un broker proprio dietro un reverse proxy TLS sulla 443.
- Configurazione di riferimento funzionante: broker `mqtt.flespi.io` porta **443**, token flespi come *username* con password vuota, TLS attivo, path `/mqtt`, metodo **wss**, proxy con "usa proxy per MQTT" attivo.

## 9.1.0 - 2026-09-05

Fix connessione a broker MQTT esterno via WebSocket-Secure dietro proxy HTTP aziendale.

### Sicurezza: rifiutati i payload in chiaro sui topic operativi
- **Vulnerabilita'**: i payload operativi sono cifrati end-to-end, ma un messaggio **non cifrato** non entrava affatto nel ramo di decifratura (`is_encrypted()` == False): veniva parsato e instradato agli handler come valido. Chi otteneva le credenziali del broker (es. copiando un QR di pairing, che contiene anche `session_id` e `device_id`) non doveva quindi rompere la cifratura per iniettare posizioni o SOS falsi — gli bastava **non usarla**.
- Un payload cifrato con la chiave sbagliata era invece gia' innocuo: fallisce la decifratura e resta `{"raw": ...}` senza campi utilizzabili.
- **Fix** in [mqtt_handler.py::_handle_message()](mqtt_handler.py): da un device accoppiato, un messaggio con `was_encrypted == False` viene scartato prima di raggiungere gli handler e loggato come warning. Nuovo contatore `plaintext_rejected` nelle statistiche.
- **Unica esenzione**: `sardroid/pairing/*` — la chiave Diffie-Hellman non e' ancora stabilita, il chiaro e' obbligatorio; il flusso esce dal percorso prima del controllo. La protezione qui e' la validazione DH lato server, non la cifratura di trasporto.
- **Chiuso anche il ramo `heartbeat` legacy**: l'heartbeat era ammesso in chiaro anche da device privi di chiave, residuo del vecchio provisioning via MQTT (oggi il provisioning avviene via QR). Restando aperto era di fatto l'unica porta rimasta per l'iniezione, dato che l'attaccante conosce `session_id` e `device_id` dal QR copiato. Verificato che nessun client attuale ne dipenda: l'app Android esce se manca `sessionId` e pubblica sempre con `encrypt=true`; `device_simulator.py::_publish()` cifra sempre, heartbeat incluso; i servizi/plugin non pubblicano su topic di sessione. Ora l'heartbeat segue la regola generale: cifrato dopo il pairing, altrimenti rifiutato.
- Il discriminante e' **strutturale** (presenza della chiave del device + tipo messaggio), non una whitelist di topic: ogni nuovo tipo di messaggio operativo e' coperto automaticamente senza modifiche.
- Nessuna modifica richiesta lato app: dopo il pairing l'app cifra gia' sempre, e l'unico invio in chiaro e' la richiesta di pairing.

### Pairing a configurazione zero: credenziali broker incluse nel QR
- Quando il broker esterno richiede autenticazione, il QR di pairing (parte 1) include ora i campi `u` (username/token) e `w` (password, omesso se vuota). L'app li legge e completa il pairing **senza chiedere nulla all'operatore** — prima ogni operatore doveva conoscere e digitare a mano le credenziali su ogni telefono.
- Implementato in [dh_service.py::generate_qr_data()](dh_service.py) e nel chiamante [server.py::create_pairing_session()](server.py). I campi compaiono solo in modalita' **external**: in embedded il broker e' anonimo.
- Retrocompatibile: i QR senza `u` continuano a far comparire il dialog di inserimento manuale. Lato app il supporto era gia' rilasciato.
- Verificato che il QR resti ampiamente entro la capacita' di lettura anche con token lunghi (~740 caratteri contro un limite di 2953 al livello di correzione L usato: margine 75%).

**Nota di sicurezza — leggere prima di usare la funzione.** I campi `u`/`w` viaggiano **in chiaro**: il QR viene letto *prima* che la chiave Diffie-Hellman sia stabilita, quindi non e' tecnicamente cifrabile end-to-end. Chi fotografa il QR ottiene le credenziali del broker.
- Mostrare il QR **solo** al momento del pairing, non lasciarlo stampato o proiettato.
- Se si sospetta una copia: cambiare le credenziali sul broker, aggiornarle in Impostazioni → Rete e rigenerare i QR. I device gia' accoppiati vanno riaccoppiati.
- Dove il broker lo consente, preferire credenziali dedicate all'esercitazione invece delle credenziali principali dell'account, cosi' una copia non compromette l'account e si revoca a fine attivita'.
- Le credenziali **non** vengono mai scritte nei log del server.
- Resta valida la cifratura end-to-end dei payload: chi ruba le credenziali puo' connettersi al broker ma **non** decifra le posizioni, la cui chiave non transita nel QR.

### Fix: pulsanti della modale di aggiornamento irraggiungibili con note lunghe
- **Bug**: con note di rilascio verbose (o con lo zoom del browser elevato) la modale "Aggiornamento Disponibile" cresceva oltre l'altezza del viewport e i pulsanti di conferma ("Piu' tardi" / "Installa e Riavvia") finivano oltre il bordo inferiore, **irraggiungibili**: l'utente non poteva ne' installare ne' rimandare l'aggiornamento. `.update-modal` non aveva alcun vincolo di altezza.
- **Fix** in [index.html](static/index.html): la modale e' ora un contenitore flex con `max-height: 90vh`; il contenuto centrale (versioni, note di rilascio, progress bar, link) e' incapsulato in un nuovo wrapper `.update-modal-body` con `overflow-y: auto`. A scorrere e' solo l'area centrale — intestazione e pulsanti restano sempre visibili e ancorati.
- Scrollbar in stile coerente con il tema scuro della dashboard; `overscroll-behavior: contain` evita che lo scroll si propaghi alla mappa sottostante a fine corsa.
- Verificato con browser headless su viewport ridotto (620x480, 12 voci di note): la barra di scorrimento compare e i pulsanti restano raggiungibili, in entrambi gli stati della modale (download manuale e installazione dopo il download).

### Fix: note di aggiornamento mostrate in markdown grezzo
- **Bug**: le note di rilascio nella modale di aggiornamento venivano inserite con `<li>${n}</li>`, cioe' come testo non interpretato. Poiche' provengono dal CHANGELOG, l'utente vedeva letteralmente `**asterischi**`, i backtick del codice e i link in forma `[testo](url)` — praticamente illeggibili.
- **Fix** in [index.html](static/index.html): aggiunto un convertitore markdown minimale per il sottoinsieme effettivamente usato nelle note — `**grassetto**`, `*corsivo*`, `` `codice` `` (reso con sfondo scuro) e `[testo](url)`.
- I link vengono resi cliccabili **solo** se `http(s)`; i link relativi a file del repo (es. `[mqtt_handler.py](mqtt_handler.py)`) diventano testo semplice, perche' come URL non porterebbero da nessuna parte.
- **Sicurezza**: l'escaping HTML avviene *prima* della conversione markdown, quindi eventuali tag presenti nel manifest restano testo inerte e non vengono eseguiti. Verificato con payload di prova (`<img onerror=...>`, `<script>`): resi come testo.

### Fix: crash all'avvio in dev mode dopo un cambio di IP
- **Bug**: al primo avvio dopo un cambio di indirizzo IP della macchina, il server terminava con `UnicodeEncodeError` prima ancora di partire. Il cambio di IP innesca la rigenerazione dei certificati SSL, che stampa `IP cambiato: <vecchio> -> <nuovo>`: quel messaggio conteneva il carattere `→` (U+2192), non rappresentabile nella codepage cp1252 usata dalla console Windows.
- Il difetto era latente da sempre ed emergeva **solo lanciando `start_dev.bat`**: nell'exe compilato in modalita' windowed `sys.stdout` viene sostituito da uno stream nullo ([server.py](server.py) righe 13-23), che ingoiava il messaggio mascherando il problema.
- **Fix** in [ssl_utils.py](ssl_utils.py): `→` sostituito con `->`. Verificati tutti i sorgenti per lo stesso rischio: i restanti caratteri non-cp1252 sono in commenti, docstring o etichette Tkinter (che gestisce Unicode nativamente), nessuno dentro una `print()`.
- **Irrobustimento** in [start_dev.bat](start_dev.bat): aggiunto `set PYTHONIOENCODING=utf-8` prima del lancio, cosi' un qualsiasi carattere non-ASCII reintrodotto in futuro in una `print()` non potra' piu' impedire l'avvio del server. Effetto collaterale voluto: `logs\server.log` diventa UTF-8 e gli accenti italiani nei messaggi restano corretti.

### Fix: broker esterno su porta non standard ignorata in modalita' WebSocket
- **Bug**: con broker esterno e connessione WebSocket, il client si collegava sempre alla porta di `mqtt.connection.ws_port` (default **8080**) ignorando la porta scelta dall'utente in `mqtt.external.port`. Su reti aziendali dove il proxy consente il `CONNECT` **solo verso la 443** (caso reale: server VVF Piemonte dietro Squid) la connessione falliva sempre, e la diagnostica ricadeva sul default `localhost:1883` di `server_config.ini` — facendo sembrare che la configurazione della UI non venisse salvata (in realta' era gia' persistita correttamente).
- **Fix** in [mqtt_handler.py::connect_client()](mqtt_handler.py): la porta di `mqtt.external.port` ha ora la precedenza su `mqtt.connection.ws_port`; nel ramo WEBSOCKET il fallback e' `websocket_port → port → 8080`.

### Fix: auto-detection WebSocket provava solo la porta 8080 in chiaro
- L'auto-detection testava il WebSocket con porta **8080 hardcoded** e schema `http://` fisso: sulle porte TLS il test falliva sempre, quindi il metodo WEBSOCKET non veniva mai raccomandato.
- **Fix** in [mqtt_handler.py::detect_network_context()](mqtt_handler.py): prova in ordine la porta configurata, poi 443, 8083, 8080. `_test_websocket()` usa `https://` sulle porte TLS (443/8084/8883), accetta il path come parametro e registra il proxy anche per lo schema `https` (prima era mappato solo `http`, quindi le richieste TLS non passavano dal proxy).

### Fix: TLS e path WebSocket non configurabili
- Il path WebSocket era hardcoded a `/mqtt`, non utilizzabile con broker che espongono `/` o `/ws`. Aggiunto il setting `mqtt.external.ws_path` con relativo campo in Impostazioni → Rete.
- Il flag TLS veniva letto solo da `mqtt.external.use_tls`, mentre la UI salva **anche** `mqtt.connection.use_tls` dal tab Proxy: i due valori potevano divergere e il TLS restava spento. Ora basta che uno dei due sia attivo.
- Sulle porte 443/8084/8883 il TLS viene forzato: li' il WebSocket e' per definizione `wss` e senza TLS l'handshake falliva senza un errore comprensibile.

### Sicurezza: verifica dei certificati TLS ora attiva di default
- **Prima**: ogni connessione TLS usava `CERT_NONE` + `tls_insecure_set(True)` — il traffico era cifrato ma il broker **non veniva autenticato**. Chi controlla il percorso di rete (tipicamente il proxy aziendale) poteva presentarsi come il broker e intercettare le credenziali, token compresi.
- **Ora**: nuovo helper `_configure_tls()` con `CERT_REQUIRED` di default. Per i broker interni con certificato self-signed e' disponibile l'opt-out esplicito `mqtt.external.tls_insecure` (casella in Impostazioni → Rete), che logga un warning quando attivo.
- **Impatto**: nessuno per chi usa broker pubblici (flespi, HiveMQ, EMQX), che hanno certificati validi. Chi usa un broker con certificato self-signed deve spuntare la nuova casella.

### Fix: autenticazione con token senza password
- La condizione `if username and password` impediva di autenticarsi ai broker che usano un **token come username senza password** (es. flespi): senza password le credenziali non venivano inviate affatto. Ora e' sufficiente lo username.

### UI e i18n
- Aggiunti in Impostazioni → Rete i campi "Path WebSocket" e "Accetta certificati non verificati", registrati in `settingsConfig` per salvataggio e ricarica.
- Nuove chiavi tradotte in `it.json`, `en.json`, `es.json`.

### Nota di verifica
- La catena `proxy CONNECT → TLS → WebSocket → MQTT` riceve ora i parametri corretti, verificata in simulazione sui percorsi di connessione (incluse 4 prove di non regressione: embedded, TCP 1883, TCP+TLS 8883, HTTP_CONNECT).
- La conferma sul campo e' arrivata con la **9.1.1**: paho-mqtt instrada correttamente il transport WebSocket attraverso il proxy HTTP CONNECT, quindi il fallback con socket pre-aperto manualmente non serve.

## 9.0.9 - 2026-07-11

Fix "Sync mappa" del filtro categoria dispositivi: ora nasconde marker 2D e trail (prima solo marker 3D).

### Fix: filtro categoria dispositivi con "Sync mappa" ora nasconde correttamente anche marker 2D e trail
- **Bug**: nel pannello dispositivi con toggle "Sync mappa" attivo, nascondere una categoria (es. "Aeromobili") non nascondeva effettivamente i tracker sulla mappa:
  - In **2D Leaflet** i marker restavano completamente visibili (il filtro non toccava affatto la mappa 2D)
  - In **3D Cesium** i marker principali sparivano ma le **trail** (polyline aircraft/vessel) restavano visibili — sia in 2D che in 3D
- **Fix** in [index.html::_applyDeviceCategoryToMap()](static/index.html): la funzione ora applica visibilita' a
  - marker Leaflet 2D via `marker.setOpacity(0)`
  - marker Cesium 3D + dropline + Aircraft3D group (gia' presente)
  - trail via nuova API `window.AircraftTrail.setVisible(deviceId, visible)`
- Aggiunta API `setVisible(deviceId, visible)` a [aircraft-trail.js](static/aircraft-trail.js) che copre sia il 2D (opacity dei segmenti polyline) che il 3D (entity.show). Uno stato `hiddenIds: Set` interno persiste attraverso i redraw del trail cosi' che un nuovo punto WS in arrivo mentre la categoria e' nascosta non faccia rispuntare la trail visibile.
- Gate aggiunto anche in `updateDeviceMarker()` cosi' un marker 2D ricreato da un position_update mentre e' nascosto nasce con opacity 0.

## 9.0.8 - 2026-07-11

Fix bug versione mostrata dopo auto-update + rifiniture UI Settings.

### Fix: versione app non aggiornata dopo auto-update
- **Bug**: dopo un auto-update, l'app mostrava ancora la vecchia versione nell'UI (osservato con 9.0.7 che continuava a mostrare 9.0.5). Motivo: `config.py::_read_version()` leggeva prima da `EXE_DIR/VERSION` (file esterno all'exe che l'auto-update NON tocca) e solo dopo dal bundle Nuitka embedded.
- **Fix**: inverto l'ordine — prima BUNDLE_DIR (embedded nell'exe, sempre allineato alla versione compilata), poi EXE_DIR come fallback.
- Aggiunto `--include-data-files=VERSION=VERSION` a `build_nuitka.py` per assicurare che VERSION sia embeddato nel bundle Nuitka (Airtrack e Aistrack lo avevano gia').
- Effetto: dopo update da 9.0.7 a 9.0.8, l'header dashboard mostrera' correttamente v9.0.8.
- Applicato lo stesso fix per omogeneita' anche a `get_current_version()` in Airtrack (1.2.3) e Aistrack (1.0.4).

### Fix visivo: toggle switch Plugin tab con knob sporgente
- Nella pagina Impostazioni → tab **Plugin**, il cerchio bianco (knob) degli switch on/off visibilmente sporgeva oltre il bordo destro del container quando attivato.
- Causa: due blocchi CSS `.toggle-switch` in conflitto in [settings.html](static/settings.html) — il blocco legacy usava `left: 23px` con `width: 44px`, il blocco nuovo aggiungeva `translateX(20px)` con `width: 46px`. Applicati entrambi il knob finiva a posizione 43px (fuori dal container di 46px).
- Fix: rimosso il blocco CSS legacy, tenuto solo il nuovo con `translateX(20px)` + colore verde `#22c55e`.

### Rename: "Traccia flotta aerea" → "Traccia tracker plugin"
- La sezione trail in Settings era chiamata "Traccia flotta aerea" perche' inizialmente copriva solo tracker `source='aircraft'` (Airtrack). Dalla 9.0.6 il gate e' stato rimosso e la trail copre anche `source='vessel'` (Aistrack) e in generale tutti i tracker plugin.
- Rinomina in [settings.html](static/settings.html) header, descrizione e hint. Aggiornati tutti e tre i file i18n (`it.json`, `en.json`, `es.json`) con testi generici che citano esplicitamente sia aeromobili che imbarcazioni.

## 9.0.7 - 2026-07-11

Rilascio di manutenzione per correggere il manifest `aggiornamento.json` verboso della 9.0.6.

### Fix: aggiornamento.json non piu' verboso
- `release.ps1` ora cap-pa le note di release a **6 bullet massimo** invece di estrarre tutti quelli del CHANGELOG. Prima con la 9.0.6 (16 bullet estratti) la modal update lato client mostrava tutti i 16 anche con il toggle "Mostra tutte" — troppo.
- Aggiunta automatica di `changelog_url` che punta al CHANGELOG completo su GitHub (`.../blob/main/CHANGELOG.md`), cosi' chi vuole leggere tutto lo storico esteso ha il link nella modal update ("Note complete →").
- Fix applicato in modo identico anche a `release.ps1` di Airtrack e Aistrack per omogeneita' della famiglia.

## 9.0.6 - 2026-07-11

Estensioni Cesium ai vascelli + attivazione plugin Aistrack + fix UX minori.

### Vascelli 3D estrusi su Cesium (parità con aircraft)
- Il modulo `Aircraft3D` era già generico e prevedeva `CATEGORY_SIZE_METERS['vessel']=30` dalla 9.0.4, ma il gate `source === 'aircraft'` nei call site di [index.html](static/index.html) e [live-map.html](static/live-map.html) bloccava l'estrusione dei tracker vessel. Rimosso il gate: ora tracker con `source='vessel'` vengono estrusi in 3D con la silhouette ogiva-scafo coerente col rendering 2D.
- `tracker-icons.js::svgPathMetaForHint()` esteso: i case `vessel_generic/vessel_sar/vessel_cargo/vessel_fishing/vessel_passenger/vessel_tanker` ora ritornano metadata `{path, viewBox, category: 'vessel'}` per l'estrusione (prima ritornava `null`).
- **Galleggiamento corretto su mare, laghi e fiumi**: il polygon vessel usa `heightReference: CLAMP_TO_GROUND` + `extrudedHeightReference: RELATIVE_TO_GROUND` cosi' galleggia sulla superficie del terrain provider Cesium (mare, Lago Maggiore ~193m, Lago di Como ~199m, Lago di Garda ~65m, ecc). Funziona automaticamente ovunque perche' Cesium World Terrain include DTM per qualsiasi superficie geografica.
- Per gli aircraft l'estrusione classica con altezza assoluta resta invariata.

### Trail rolling window esteso ai vascelli
- Il modulo `AircraftTrail` era gia' internamente generico. Rimosso il gate `data.source === 'aircraft'` nei call site di [index.html](static/index.html) e [live-map.html](static/live-map.html): ora anche i tracker vessel emettono trail (rolling window 15 min con fade opacità).
- Zero setting UI nuovi, riusa "Traccia flotta aerea" delle impostazioni. Se in futuro serve un setting dedicato per vessel, si aggiunge.
- Effetto pratico: apri dashboard con Aistrack attivo → i vascelli AIS appaiono in 3D come silhouette estrusa ciano con trail delle ultime 15 min. Aircraft e vessel hanno rendering equivalente in dashboard e live-map.

### Attivazione plugin Aistrack (in preparazione al rilascio Aistrack 1.0.0)
- Il toggle "Sardroid Aistrack" nel Tab Plugin di `settings.html` era placeholder disabled con badge "non ancora disponibile". Ora attivato: checkbox funzionante, ingest da Aistrack via `POST /api/local-ingest/positions` con `source_hint="vessel"` accettato.
- `loadPluginSettings()` in JS refactored per gestire N plugin (lista `PLUGINS = ['airtrack', 'aistrack']`) senza duplicare codice.
- Description i18n aggiornata: "Ricezione posizioni natanti via AIS (AISStream, ricevitore locale, MarineTraffic)" al posto di "in sviluppo".

### Modale aggiornamento più compatta
- La modale che appare cliccando il badge "Aggiornamento disponibile" mostra ora al massimo 6 bullet iniziali di note di rilascio, con un toggle "Mostra tutte (N)" per espandere l'intera lista.
- Nuovo campo opzionale `changelog_url` in `aggiornamento.json` — se presente, la modale mostra un link "Note complete →" che apre il CHANGELOG completo su GitHub in una nuova tab.
- Motivazione: prima con le 41+ bullet della release 9.0.5 la modale diventava uno schermo intero di testo scorrevole difficile da leggere.
- Chiavi i18n aggiunte in IT/EN/ES: `show_all`, `show_less`, `full_changelog`.
- Retro-compat: `aggiornamento.json` senza `changelog_url` funziona come prima; installazioni server pre-9.0.6 semplicemente ignorano il campo.

### Fix knob toggle-slider fuori bordo (Tab Sicurezza/Plugin)
- I toggle switch verdi di `settings.html` avevano il knob bianco che sporgeva di 1-2px oltre il bordo destro del container quando checked. Correzione `translateX(20px)` invece di `22px` per rispettare i 46px del container tenuto conto di border 1px per lato + padding 2px iniziale.

## 9.0.5 - 2026-07-11

Controllo granulare dei plugin di ingest, filtro categoria in tab Dispositivi, `vertical_class` per rendering 3D coerente, parità Aircraft3D su live-map.

### Vascelli 3D su Cesium + trail rolling window (fix UX 9.0.5)
- **Silhouette 3D estruse anche per i vascelli**: `Aircraft3D` era gia' generico (aveva `CATEGORY_SIZE_METERS['vessel']=30` da 9.0.4) ma il gate `source === 'aircraft'` nei call site di [index.html](static/index.html) e [live-map.html](static/live-map.html) impediva l'estrusione. Rimosso il gate: ora tracker con `source='vessel'` vengono estrusi in 3D usando la silhouette ogiva del vessel (coerente col rendering 2D).
- `tracker-icons.js::svgPathMetaForHint()` esteso: i case `vessel_generic/vessel_sar/vessel_cargo/vessel_fishing/vessel_passenger/vessel_tanker` ora ritornano metadata `{path: VESSEL_HULL_PATH, viewBox, category: 'vessel'}` per l'estrusione. Prima ritornava `null`.
- Preparato lato server dalla 9.0.5 (source_hint='vessel' accettato, chip categoria "Vascelli" nella dashboard, border-left ciano `#06b6d4`, `vertical_class='ground'` con CLAMP_TO_GROUND).
- **Trail rolling window (fade opacita' su 15 min) esteso ai vascelli**: il modulo `AircraftTrail` era gia' internamente generico. Rimosso il gate `data.source === 'aircraft'` nei call site di [index.html](static/index.html) e [live-map.html](static/live-map.html): ora ogni tracker aircraft O vessel emette trail. Zero setting UI nuovi, riusa "Traccia flotta aerea" delle impostazioni.
- Effetto pratico: apri dashboard con Aistrack attivo → i vascelli AIS appaiono in 3D come silhouette estrusa ciano (colore ereditato da defaults.color_vessel), con trail delle ultime 15 min. Aircraft e vessel ora hanno rendering equivalente in dashboard e live-map.

### Modale aggiornamento più compatta (fix UX 9.0.5)
- La modale che appare cliccando il badge "Aggiornamento disponibile" ora mostra al massimo 6 bullet iniziali di note di rilascio, con un toggle "Mostra tutte" per espandere l'intera lista.
- Nuovo campo opzionale `changelog_url` in `aggiornamento.json` — se presente, la modale mostra un link "Note complete →" che apre il CHANGELOG completo su GitHub in una nuova tab.
- Motivazione: prima con le 41+ bullet della release 9.0.5 la modale diventava uno schermo intero di testo scorrevole difficile da leggere.
- Il generatore `Genera_JSON_per Download_SardroidServer_da_github.html` aggiornato con il nuovo campo `changelog_url` e placeholder per note piu' concise (5-7 bullet).
- Retro-compat: `aggiornamento.json` senza `changelog_url` funziona come prima; installazioni server pre-9.0.5 semplicemente ignorano il campo.
- Chiavi i18n aggiunte in IT/EN/ES: `show_all`, `show_less`, `full_changelog`.

### Tab Plugin — interruttore per plugin
- Aggiunto per ciascun plugin (Airtrack ora, futuri Aistrack, ecc.) un toggle switch on/off che governa se il server accetta o rifiuta dati da quel plugin.
- Se il plugin è disabilitato, `POST /api/local-ingest/positions` risponde `423 Locked` con motivazione e i dati NON entrano nel DB.
- `POST /api/local-ingest/tracker-sweep` diventa un no-op silenzioso (200 vuoto) quando il relativo plugin è off — evita che sweep periodici cancellino tracker legittimi dopo un toggle temporaneo.
- I toggle sono a livello impostazioni (`plugins.<name>.enabled`), auto-salvati on-change.

### Tab Sicurezza — split permesso Posizioni
- Il vecchio permesso "Posizioni dispositivi" per l'ospite è stato diviso in due:
  - **Dispositivi Sardroid** — telefoni pairati (source='device')
  - **Tracker da plugin** — aeromobili, natanti, beacon (source='aircraft'/'vessel'/'beacon')
- I 7 checkbox della sezione Permessi Ospite sono ora **toggle switch verdi** coerenti col resto delle impostazioni.
- Auto-save on change: rimosso il tasto "Salva Permessi Ospite".
- Migrazione retro-compatibile: se il server riceve un payload legacy con solo `positions`, sia `devices` che `trackers` ereditano quel valore (nessuna rottura per client non aggiornati).
- La live-map filtra ora granularmente per categoria in base ai due nuovi flag (SOS resta legato a `devices` — gli SOS arrivano solo dai telefoni).

### Vertical class per rendering 3D
- Nuovo campo opzionale `vertical_class` nel modello `TrackerPositionEntry` con valori `"airborne"`, `"ground"`, `"submerged"`.
- Se il plugin non fornisce il valore, il server lo deduce dal `source_hint` (`aircraft`→airborne, altro→ground). Retrocompatibilità piena con Airtrack 1.1.0.
- Il campo è propagato nel broadcast WebSocket `position_update` verso tutti i client.
- Cesium 3D (index.html + live-map.html) usa `vertical_class` per decidere `heightReference`:
  - `airborne` + altitudine ≥30m → posizione assoluta (in volo);
  - `airborne` + altitudine <30m → clamp al terreno (in decollo/atterraggio, evita di seppellire il modello sotto il DTM);
  - `ground` → clamp al terreno con altitudine 0;
  - `submerged` → posizione assoluta con altitudine negativa.
- Dropline generalizzata: appare per `airborne` (dal muso verso il suolo) o `submerged` (dalla superficie verso il basso), non più solo per aircraft.

### Filtro categoria in tab Dispositivi (dashboard)
- Nuova barra chip sopra la lista device: **Tutti / Telefoni / Aerei / Vascelli / Beacon / Sconosciuti**. I chip vuoti (categorie senza device) non appaiono; ogni chip ha un contatore.
- Barra verticale colorata a sinistra sulle card device per categoria (verde/blu/ciano/ambra/grigio).
- Toggle "Sync mappa" opt-in: quando attivo, il filtro chip nasconde/mostra anche i marker Cesium (billboard, silhouette 3D, dropline) delle categorie fuori scope.
- Filtro testuale e filtro chip sono ortogonali: agiscono via due classi CSS distinte (`search-hidden`, `cat-hidden`) e si compongono senza collidere.
- Auto-switch a **Telefoni** all'arrivo di un SOS: se il chip corrente è su un'altra categoria (aerei, vascelli, ecc.), il filtro passa automaticamente a "Telefoni" — l'SOS resta immediatamente visibile in lista e sulla mappa. Nessun timer, nessun toast, nessun ripristino automatico.

### Parità Aircraft3D su live-map
- Il modulo `aircraft-3d.js` (silhouette estrusa) è ora caricato anche in `live-map.html`: gli ospiti vedono gli aeromobili come mesh 3D con lo stesso rendering della dashboard operatore.
- Le silhouette SVG (airliner, elicottero, elicottero antincendio, unknown, vessel) sono state estratte in un nuovo modulo condiviso [`static/tracker-icons.js`](static/tracker-icons.js) (`window.TrackerIcons`) usato sia da dashboard sia da live-map — zero duplicazione, una sola cache PNG per Cesium billboard.
- La live-map ora rende i tracker con billboard silhouette + heightReference dinamico + rotazione heading compensata sulla rotazione camera, allineata al comportamento operatore.
- `Aircraft3D.setGroupVisible(deviceId, visible)`: nuovo helper per mostrare/nascondere la silhouette senza distruggerla (usato dal toggle "Sync mappa" della dashboard).

### Lifecycle tracker piu' robusto (fix flicker, adattivo al poll rate)
- Il plugin (Airtrack 1.2.0+) dichiara al server il proprio `poll_interval_seconds` nel body di `POST /api/local-ingest/tracker-sweep`. Il server ne deriva:
  - **grace sweep** = `max(15s, poll * 1.5)` — i tracker con position recente sono preservati anche se il plugin dice "non li ho": un aircraft che esce temporaneamente dagli entries (state OpenSky con lat/lon null, velocity sotto soglia per un tick) non viene cancellato subito. Riappare al poll successivo senza destroy+re-create.
  - **stale timeout** (safety net) = `max(90s, poll * 5)` — 5 poll consecutivi mancati = plugin morto. Prima era 60s hardcoded: un singolo hiccup di rete o rate-limit OpenSky cancellava tracker validi.
- Retro-compat: se il plugin non dichiara `poll_interval_seconds` (Airtrack < 1.2.0), il server usa i default legacy 45s/180s.
- Il timing del poll dell'utente e' rispettato: se aumenti a 60s in Airtrack, grace diventa 90s e stale 300s automaticamente.
- Effetto pratico: fine del flicker "aereo sparisce dalla lista/mappa 3D e ricompare dopo pochi secondi" osservato in produzione durante i test 9.0.5.

### i18n IT / EN / ES
- Aggiunte chiavi `settings.plugins.airtrack_desc`, `aistrack_desc`, `coming_soon`, `toggle_title`, `toggle_hint` (Tab Plugin).
- Aggiunte `settings.security.perm_devices`, `perm_trackers` (Tab Sicurezza).
- Aggiunte `dashboard.devices.chip_all`, `chip_device`, `chip_aircraft`, `chip_vessel`, `chip_beacon`, `chip_unknown`, `sync_map` (barra chip Dispositivi).
- Completate chiavi pre-esistenti mancanti: `settings.trails.*` (sezione Traccia flotta aerea) e `dashboard.pairing_modal.validity_label`/`validity_value` (modale pairing).
- Parità completa IT/EN/ES a 1220 chiavi ciascuna.

## 9.0.4 - 2026-07-08

Pairing "1 QR alla volta, senza scadenza" + aeromobili 3D estrusi in vista Cesium.

### Pairing
- Rimossa la scadenza dei 5 minuti. Il QR resta valido finché non chiudi la finestra, completi il primo pairing, cambi sessione Sardroid, o generi un nuovo QR.
- Cap "1 QR alla volta": una nuova richiesta di QR cestina automaticamente eventuale token pending pre-esistente. Nessun multi-token simultaneo.
- Chiusura della modale ora invalida il token server-side subito (evita orfani in memoria).
- Se sono aperte più dashboard, quando una genera un nuovo QR le altre chiudono la modale zombie con toast di avviso.
- Cambio o creazione di nuova sessione Sardroid invalida automaticamente eventuale QR pending (i QR contengono session_id, quindi puntavano alla sessione precedente).
- Testo modale aggiornato: label "Validità" con valore "finché questa finestra resta aperta" al posto del countdown.

### Aeromobili 3D in Cesium
- Le silhouette SVG degli aeromobili (aereo generico, Canadair, elicottero, elicottero antincendio) sono ora estruse come poligoni 3D nella vista Cesium, con volume verticale visibile in vista obliqua.
- Coerenza visiva 2D↔3D: guardando la mappa dall'alto la silhouette è identica al rendering Leaflet. Ruotando la camera si vede il volume.
- Colore preso da `device.color` (uguale al 2D).
- Orientation dinamica: la mesh ruota in base all'heading GPS reale.
- Scaling adattivo camera-distance: a distanze < 5 km scala reale (~50m aereo, 25m elicottero), oltre cresce linearmente per rimanere sempre visibile senza sparire come puntino.
- Update in-place: cambi di posizione/heading non ricreano la mesh.
- Fallback trasparente: se il modulo `Aircraft3D` non è disponibile o non riesce a parsare il path, il rendering ricade sul billboard 2D screen-space come prima.

### Riorganizzazione settings UI
- Nuova tab **Plugin** in `settings.html` dedicata alle configurazioni dei plugin di ingest locali (Airtrack, futuri Aistrack).
- Sezione "Traccia flotta aerea" spostata dal tab Mappe al nuovo tab Plugin, coerente con il fatto che riguarda dati provenienti dal plugin Airtrack.
- Introduzione del tab con hint sull'endpoint `/api/local-ingest/positions` usato dai plugin.
- Chiavi i18n IT/EN/ES aggiornate.

## 9.0.3 - 2026-07-06

Major Update - Aircraft Inject.

- Nuovo endpoint `/api/local-ingest/positions` per plugin locali (Sardroid Airtrack, futuri Aistrack).
- Nuovo endpoint `/api/local-ingest/tracker-sweep` per rimozione immediata dei tracker fuori bbox.
- Discriminazione tracker vs device: i tracker non finiscono nel timer offline 60s.
- Rendering trackers su mappa 2D Leaflet, 3D Cesium, live-map con icone e colori configurabili.
- Vari fix vari alla pipeline georef, UI e logging.
