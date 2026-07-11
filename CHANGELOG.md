# Sardroid Server — Changelog

## 9.0.5 - 2026-07-11

Controllo granulare dei plugin di ingest, filtro categoria in tab Dispositivi, `vertical_class` per rendering 3D coerente, parità Aircraft3D su live-map.

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
