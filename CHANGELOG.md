# Sardroid Server — Changelog

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
