# Sardroid Server — Changelog

## 9.5.7 - 2026-10-26

Ricerca di luoghi sulle mappe e leggibilita' delle impostazioni nel tema
chiaro.

### Ricerca di luoghi e coordinate

- Le mappe 2D e 3D hanno ora una casella di ricerca in alto al centro:
  si scrive un toponimo e la mappa ci va. Durante un intervento il nome
  di una localita' arriva per radio, e finora andava cercato a mano
  trascinando e ingrandendo.
- La stessa casella accetta le **coordinate incollate**, nelle notazioni
  che si usano sul campo: gradi decimali, gradi e primi, gradi primi e
  secondi, con o senza simboli, con il punto cardinale davanti o dietro,
  con la virgola decimale all'italiana. `45.4101, 7.5771` e
  `45°24'36.4"N 7°34'37.6"E` portano allo stesso punto.
- La ricerca c'e' anche nella finestra "Scarica mappa offline", dove
  l'area da scaricare si trovava trascinando la mappa dall'Italia
  intera.
- Nella georeferenziazione raster, dove la ricerca per nome c'era gia',
  ora si possono incollare anche le coordinate.
- Il servizio e' quello di OpenStreetMap e passa dal proxy del server,
  quindi funziona anche dietro una rete aziendale. **Senza connessione**
  la ricerca per nome lo segnala e nient'altro cambia: mappa,
  selezione dell'area e scarico restano quelli di prima, e le coordinate
  continuano a funzionare perche' non interrogano nessun servizio.

### Tema chiaro: leggibilita' delle impostazioni

- Le finestre delle mappe offline erano rimaste fuori dalla conversione
  al tema: restavano scure dentro una pagina chiara, con alcune
  etichette al limite della leggibilita'.
- I **titoli delle opzioni** nelle schede Plugin e Sicurezza erano
  grigio chiarissimo su pannello bianco, quindi quasi invisibili.
- Stesso difetto nel pulsante di chiusura della finestra donazione,
  nella riga di avanzamento della georeferenziazione assistita,
  nell'anteprima MBTiles e nel referto dei test di rete.
- Il selettore dell'interfaccia di rete mancante aveva un fondo scuro
  fisso con testo che seguiva il tema: il nome dell'interfaccia non si
  leggeva.
- Tre pulsanti - fra cui "Genera MBTiles" - usavano uno stile mai
  definito e apparivano come testo senza sfondo in entrambi i temi.

## 9.5.6 - 2026-10-26

Gli elenchi laterali portano all'elemento scelto, in entrambe le viste.

### Click sulle voci degli elenchi

- Cliccando una voce - dispositivo, waypoint, foto - ora si apre anche
  la sua scheda, non solo si sposta la mappa. Prima bisognava ritrovare
  l'elemento a video e centrare la mira sul segnaposto. Vale in 2D e in
  3D, come gia' per le tracce.
- Le **aree** non venivano inquadrate affatto nella vista 3D: il
  comando agiva solo sulla mappa 2D. Ora l'area si inquadra in entrambe,
  a una distanza che tiene conto della sua estensione.
- Il **dispositivo** si apriva a una distanza molto maggiore degli altri
  elementi dell'elenco, e diversa fra 2D e 3D. Ora e' la stessa delle
  foto.
- L'elemento non finisce piu' di lato: aprendo una scheda la mappa si
  sposta per farla stare tutta, e la posizione veniva persa.

### Vista 3D: primo posizionamento sottoterra

- Alla prima apertura della pagina, il primo dispositivo o waypoint
  scelto dall'elenco poteva mostrare uno schermo scuro: la camera
  finiva sotto la superficie. I segnaposto sono registrati a due metri
  sul livello del mare e si appoggiano al terreno solo nel disegno,
  quindi in montagna il punto di arrivo cadeva centinaia di metri sotto
  la quota reale. Ora la quota del terreno viene misurata prima di
  spostare la vista.
- Dal secondo click in poi non si notava, perche' il terreno era ormai
  caricato.

### Tracce

- Dopo "Nascondi tutte", cliccando una traccia nell'elenco tornava in
  mappa senza essere evidenziata: la selezione precedente non veniva
  chiusa, e il comando la considerava gia' scelta. Il "Nascondi" della
  singola traccia lo faceva gia' correttamente.

## 9.5.5 - 2026-10-26

Le tracce si scelgono dall'elenco come dalla mappa.

### Selezione dall'elenco delle tracce

- Cliccando una traccia nell'elenco laterale, ora si evidenzia sulla
  mappa e compare il riquadro con distanza, durata, velocita' e numero
  di punti - lo stesso che si otteneva solo cliccandola sulla mappa.
  Prima il percorso veniva soltanto inquadrato.
- Vale nella vista 2D e nella 3D.
- Ricliccare la stessa voce dell'elenco riporta sulla sua traccia senza
  deselezionarla: liberare la selezione resta un gesto della mappa,
  dove si riclicca la linea.

## 9.5.4 - 2026-10-26

Selezione delle tracce nella vista 3D.

### Dati della traccia in 3D

- Cliccando in 3D un tratto di percorso oltre il primo, il riquadro dei
  dati compariva vuoto: i tratti separati hanno un identificativo
  proprio, e chi riceveva il click non risaliva alla traccia.
- Evidenziazione della traccia e riquadro dei dati ora compaiono e
  spariscono sempre insieme. Passando da 2D a 3D il riquadro restava
  aperto mentre l'evidenziazione se ne andava, e il riquadro finiva per
  riferirsi a una traccia che nulla indicava piu' sulla mappa.
- Chiudendo la selezione senza che i dati fossero disponibili, la
  traccia restava evidenziata: ora la selezione si chiude per intero.
- Scegliendo un'altra traccia mentre i dati della precedente erano
  ancora in arrivo, la risposta tardiva poteva sostituire il riquadro
  appena aperto.

## 9.5.3 - 2026-10-26

Zona silenziosa nelle viste 3D e card della galleria foto.

### Zona silenziosa: tracce e pin nelle viste 3D

- Nella vista 3D una traccia che entrava e usciva dal cerchio veniva
  ricongiunta con una linea dritta che lo attraversava: proprio
  l'ingombro che la zona doveva togliere. Ora i tratti restano separati.
  Sui dati di un intervento reale, con raggio 50 m, sparivano cosi' tre
  linee di troppo.
- I pin di inizio e fine percorso restavano dentro il cerchio anche
  quando la traccia non si vedeva piu'. Ora seguono la zona come il
  resto: nello stesso intervento erano dodici.
- I due capi si valutano separatamente: una squadra partita dal posto
  comando ha l'inizio dentro la zona, ma il pin di arrivo - che dice
  dov'e' finita - resta visibile.
- Una traccia mostrata **dopo** aver impostato la zona nasce ora gia'
  ritagliata, in tutte le viste. Prima compariva intera e si sistemava
  solo toccando il raggio.
- Stessa correzione nella mappa ospiti.

### Zona silenziosa: interruttore nelle viste 3D

- L'interruttore e' ora anche nella vista 3D e nella mappa ospiti: per
  spegnere la zona non serve piu' tornare in 2D, cambiarla e tornare
  indietro.
- In 3D la card sta a destra degli altri comandi, nella stessa posizione
  che ha in 2D: la stessa cosa si cerca nello stesso posto in entrambe
  le viste.
- La card della vista 2D non resta piu' visibile sotto i comandi 3D.
- Raggio e centro restano nella vista 2D. Il raggio si giudica male in
  prospettiva, e spostare il centro richiede un click sul terreno, che
  in 3D fa gia' altro.
- Nella mappa ospiti la scelta di chi guarda prevale su quella della
  sala operativa: spegnendo la zona resta spenta, mentre centro e raggio
  continuano ad arrivare dalla dashboard, perche' dicono dove si sta
  nascondendo. Ricaricando la pagina si torna a seguire la dashboard.

### Card della galleria foto

- Le miniature cambiavano dimensione da una card all'altra: un nome
  lungo mandava l'ora a capo, e una foto senza nota aveva una riga in
  meno. Ora la struttura e' la stessa per tutte e le miniature sono
  tutte uguali.
- La card mostra chi ha scattato e la nota, una per riga; data e ora
  sono in un piccolo riquadro sulla miniatura, su fascia scura perche'
  la foto sotto puo' essere chiara. Il nome ha cosi' la riga per se' e
  non si accorcia piu' con i puntini.
- La quota non e' piu' nella card. Tutti i dati - compresi quota,
  coordinate e direzione di scatto - restano nel suggerimento al
  passaggio del mouse e sotto la foto aperta.
- Le card sono un po' piu' alte, e lo spazio guadagnato va alla
  miniatura: da circa 87 a 117 pixel di altezza utile.
- I dati sotto la foto aperta erano quasi illeggibili nel tema chiaro:
  il fondo li' e' nero in entrambi i temi, ma il testo seguiva la
  tavolozza e diventava nero su nero.

## 9.5.2 - 2026-10-25

Leggibilita' del tema chiaro e miniature delle foto gia' in archivio.

### Messaggi di avviso illeggibili nel tema chiaro

- Gli avvisi che compaiono in alto a destra avevano testo bianco su fondo
  pastello: si leggeva il colore, non le parole. Il contrasto era circa
  un quarto del minimo leggibile.
- Il testo segue ora il tema. Il tipo di avviso - informazione, buon
  esito, attenzione, errore - resta riconoscibile dal fondo colorato e
  dalla barra laterale, come nel tema scuro.

### Miniature delle foto gia' in archivio

- Le miniature si generano all'arrivo della foto, quindi quelle ricevute
  prima del 9.5.0 erano rimaste a 100 pixel sul lato lungo: nelle card
  della galleria venivano ingrandite di quattro volte e si vedevano
  sgranate.
- Per le sessioni di questa installazione sono state rifatte
  dall'immagine piena, che resta sempre in archivio, con gli stessi
  parametri usati alla ricezione: una foto vecchia e una nuova risultano
  ora identiche.
- Chi aggiorna da una versione precedente **continuera' a vedere sgranate
  le foto gia' ricevute**: il server non rigenera le miniature da solo.
  Le foto nuove sono a piena definizione fin dal 9.5.0.

## 9.5.1 - 2026-10-25

Due correzioni: i dispositivi non venivano piu' visti dopo un cambio di
sessione, e lo spegnimento era illeggibile nel tema chiaro.

### Dispositivi invisibili dopo un cambio di sessione

- Attivando o importando un'altra sessione a server avviato, i
  dispositivi collegati risultavano **tutti offline** pur continuando a
  trasmettere. Il server restava in ascolto sui canali della sessione
  precedente: connesso, ma sordo.
- Il sintomo era insidioso perche' non assomigliava a un guasto. Nessun
  errore, stato MQTT "connesso", e i dispositivi semplicemente spenti.
  L'unico rimedio era riavviare il server.
- Ora il cambio di sessione aggiorna i canali di ascolto senza
  riconnettersi. Se l'operazione non riesce resta una segnalazione nel
  log, cosi' il problema non e' piu' muto.

### Tema chiaro: leggibilita'

- La conferma di spegnimento aveva testo giallo pallido e grigio chiaro,
  scelti quando il tema era solo scuro: sul fondo chiaro erano
  illeggibili. E' la finestra che chiede conferma prima di spegnere
  tutto, e deve essere la piu' leggibile della dashboard.
- Stesso difetto nei **campi di scrittura**: ricerca tracce, filtro,
  casella del messaggio, scelta del destinatario, nome dispositivo e
  telefono. Si scriveva in bianco su bianco.
- Corretti anche le note di rilascio della finestra di aggiornamento e il
  nome scelto sul dispositivo.

## 9.5.0 - 2026-10-25

Zona silenziosa attorno al posto comando, tema chiaro, filtri della barra
laterale riorganizzati, import GeoJSON con coordinate proiettate.

### Zona silenziosa

- Una card sulla mappa definisce un cerchio attorno al posto comando:
  dentro, tracce e foto non vengono disegnate. Attorno al PC si accumulano
  decine di passaggi che non dicono nulla e coprono il resto della mappa.
- Della traccia si nasconde **solo il tratto interno**: una squadra che
  parte dal posto comando resta tracciabile da quando ne esce.
- Si imposta dalla vista 2D; la 3D e la mappa ospiti la ereditano.
  L'impostazione resta al cambio vista e al riavvio del browser: durante
  un intervento la zona e' quella e non cambia.
- Spegnere l'interruttore non cancella centro e raggio: servono per
  guardare sotto un momento, non per rifare tutto da capo.
- Il centro si sposta premendo "Sposta centro" e poi un punto della mappa.
  Serve il passaggio esplicito, altrimenti ogni click lo muoverebbe e non
  si potrebbe piu' selezionare una traccia.

### Tema chiaro

- Un pulsante in testata passa fra tema scuro e chiaro, nella dashboard e
  nella pagina impostazioni. La scelta e' condivisa fra le due pagine e
  viene ricordata. La mappa ospiti resta scura: e' quasi tutta
  cartografia, e il tema non cambierebbe nulla di visibile.
- Dalla mappa ospiti e' stato tolto anche il selettore di lingua: le
  poche etichette seguono la lingua scelta nella dashboard, che e' dove
  la scelta conta davvero.
- I colori dell'interfaccia sono ora definiti in un unico punto per
  **ruolo** - sfondo di una card, testo attenuato, stato di errore - invece
  che ripetuti tinta per tinta.
- I colori di mappa, tracce e dispositivi non seguono il tema: servono a
  riconoscere le squadre e devono restare quelli.

### Filtri della barra laterale

- Il filtro per dispositivo vale ora **anche per la mappa** in tutte le
  schede. Prima lo stesso comando si comportava in quattro modi diversi:
  in Foto e Tracce l'elenco si riduceva e i segnaposto restavano tutti.
- Un'unica casella di ricerca per scheda, al posto delle tendine. Cerca
  nel contenuto e nei dati del dispositivo, **compreso il numero di
  telefono**: in operazione spesso si ha in mano quello e non il nome.
- Nei Dispositivi si filtra anche per stato: Online, Offline, SOS.
- Pulsante "Azzera filtri" uguale in tutte le schede, visibile solo quando
  c'e' qualcosa da azzerare.
- Il contatore in testata dice "3 di 12" quando un filtro e' attivo: prima
  in una scheda mostrava il totale e contraddiceva cio' che era a schermo.
- La scheda Aree, che era l'unica priva di filtro per dispositivo pur
  avendone il dato, e' stata allineata alle altre.

### Tracce: dati e selezione sulla mappa

- Cliccando una traccia compare un riquadro con dispositivo, distanza,
  durata, velocita' e numero di punti. Vale in 2D e in 3D.
- La traccia selezionata si distingue per spessore e alone chiaro,
  mantenendo il colore del dispositivo.

### Foto

- Le miniature passano da 100 a 400 pixel: sulle card erano ingrandite
  fino a otto volte e si vedevano sgranate. Quelle gia' in archivio sono
  state rigenerate.
- Le card danno spazio all'immagine e mettono in evidenza la nota, che e'
  cio' che dice di che foto si tratta. Le coordinate restano nel
  suggerimento e nella finestra di dettaglio.
- I segnaposto sulla mappa non sono piu' ruotati secondo l'angolo di
  scatto: a quaranta gradi la miniatura era illeggibile e i segnaposto
  vicini si accavallavano, senza che la direzione si capisse comunque. Ora
  e' scritta in chiaro fra i dati, in gradi e punto cardinale.

### Messaggi

- Un messaggio si puo' eliminare singolarmente: prima si poteva solo
  svuotare l'intera chat.

### Import: coordinate proiettate e nomi delle zone

- Un GeoJSON che dichiara un sistema di riferimento diverso da WGS84 -
  tipicamente UTM, come fanno gli export cartografici italiani - viene
  ora **riproiettato**. Prima quelle coordinate erano lette come gradi e
  le zone finivano fuori dal mondo.
- Il nome della zona viene cercato anche nei campi che gli export usano
  davvero (NOME, Label, title...) e, se non ce n'e' nessuno, nel nome del
  file: meglio di "Zona importata 1".
- Un file .kml che in realta' e' un archivio compresso viene riconosciuto
  dal contenuto: diversi portali cartografici li distribuiscono cosi'.

### Comandi al dispositivo

- **Richiedi posizione** e **risincronizza traccia** venivano cifrati con
  la chiave sbagliata e il dispositivo li scartava in silenzio. Ora usano
  la chiave del dispositivo. (L'app non gestisce ancora questi comandi: la
  correzione riguarda il trasporto.)

### Mappe offline

- Il vincolo sul formato `.mbtiles` e' ora indicato accanto al selettore
  della mappa, non solo in cima alla pagina: chi copia un file nella
  cartella scopriva solo dall'elenco vuoto che il formato era sbagliato.

### Impostazioni

- Titolo e schede restano in alto mentre si scorre: nelle sezioni lunghe
  si perdeva di vista in quale scheda si stava lavorando.


## 9.4.0 - 2026-10-24

Messaggi urgenti con conferma di lettura, nome dei dispositivi deciso dalla
sala operativa, dati delle tracce cliccabili sulla mappa.

### Messaggi urgenti

- Un messaggio puo' essere inviato come **urgente**: sul dispositivo fa
  scattare un allarme sonoro e una schermata a tutto schermo che restano
  attivi finche' l'operatore non conferma di aver letto. Suona anche a
  telefono silenziato e a schermo bloccato, e riaccende lo schermo.
- La casella "Urgente" chiede conferma prima dell'invio e si disattiva da
  sola dopo: un urgente lasciato acceso per distrazione farebbe suonare
  allarmi per messaggi di servizio, e dopo qualche falso allarme nessuno ci
  farebbe piu' caso.
- In chat ogni urgente mostra **chi ha letto e chi no**, aggiornato in tempo
  reale senza ricaricare la pagina. I dispositivi che non si vedono da piu'
  di un giorno sono indicati a parte come non raggiungibili: restare in
  eterna attesa di una loro conferma renderebbe l'indicazione inutile.
- I messaggi vengono conservati anche sull'app e sopravvivono al riavvio.
  Un urgente non ancora confermato resta pendente: l'allarme riparte se
  l'app viene chiusa e riaperta.

### Nome dei dispositivi

- Il nome assegnato dalla dashboard **non viene piu' sovrascritto** dal
  dispositivo. Prima ogni heartbeat riportava il nome scelto dal
  proprietario del telefono, quindi rinominare un mezzo sembrava funzionare
  e dopo pochi secondi tornava tutto come prima.
- La scelta della sala operativa non e' definitiva: **svuotando il campo**
  si torna al nome del dispositivo, e un nuovo pairing riparte sempre da
  quello. Il dialogo di rinomina mostra il nome scelto sul dispositivo
  quando differisce.

### Tracce: dati e evidenziazione sulla mappa

- Cliccando una traccia, in 2D e in 3D, compare un riquadro con dispositivo,
  distanza, durata, velocita' media e massima, numero di punti, inizio e
  fine. Prima quei dati erano solo nell'elenco laterale, non raggiungibili
  dalla mappa, dove si lavora.
- La traccia selezionata si distingue per **spessore e alone chiaro**,
  mantenendo il colore del dispositivo. Un click a vuoto, o su un altro
  elemento della mappa, chiude la selezione.

### Fix: i comandi al dispositivo non arrivavano

- **Richiedi posizione** e **risincronizza traccia** venivano cifrati con la
  chiave sbagliata: il dispositivo li riceveva, falliva la decifratura e li
  scartava in silenzio. Ora usano la chiave del dispositivo, come i
  messaggi. (L'app non gestisce ancora questi comandi: la correzione
  riguarda il trasporto.)

### Fix: messaggi di servizio in chat

- I comandi interni inviati al dispositivo finivano fra i messaggi salvati e
  nel conteggio dei non letti, pur non essendo visibili in elenco. Ora
  vengono riconosciuti e tenuti fuori.

### Fix: avviso sulla chat non piu' veritiero

- L'avviso diceva che i messaggi non vengono conservati: non e' piu' vero,
  la chat ha una propria persistenza su entrambi i lati. Resta vero il
  limite che conta: un messaggio inviato mentre l'app e' chiusa non arriva
  e non viene recuperato. Un avviso che dice il falso e' peggio di nessun
  avviso, perche' fa perdere fiducia anche nella parte vera.

### App Sardroid Mobile

- Nella schermata Messaggi l'elenco viene letto dall'archivio locale, quindi
  **sopravvive alla chiusura dell'app**. Prima era una lista in memoria,
  limitata a 100 messaggi e persa a ogni riavvio.
- In impostazioni il pulsante di salvataggio di nome e telefono dice cosa
  sta succedendo: **floppy ambra** se ci sono modifiche da salvare, **spunta
  verde** quando sono salvate. Prima passava da un colore all'altro senza
  segnalare l'avvenuto salvataggio.


## 9.3.7 - 2026-10-23

### Nuova APK Sardroid Mobile

- Aggiornata l'app Android distribuita insieme al server: si scarica dalla
  dashboard e viene estratta accanto all'eseguibile al primo avvio. Chi ha
  gia' l'app installata ricevera' la nuova versione dal server.
- Nessuna modifica al server rispetto alla 9.3.6.


## 9.3.6 - 2026-10-22

### Nuova APK Sardroid Mobile

- Aggiornata l'app Android distribuita insieme al server: si scarica dalla
  dashboard e viene estratta accanto all'eseguibile al primo avvio. Chi ha
  gia' l'app installata ricevera' la nuova versione dal server.
- Nessuna modifica al server rispetto alla 9.3.5.


## 9.3.5 - 2026-10-15

Il dialogo di riavvio poteva non comparire, o restare in attesa mentre il
server era gia' tornato disponibile.

### Fix: il dialogo di riavvio non compariva in un caso

- Cambiando i **Servizi esposti** insieme alla **modalita' broker**, il
  dialogo non veniva mostrato affatto: il controllo finiva in un ramo diverso
  del codice. Le impostazioni venivano salvate ma nessuno avvisava che
  servisse un riavvio per applicarle, cioe' proprio la situazione che il
  dialogo esiste per evitare.

### Fix: l'attesa del riavvio poteva fallire a server gia' attivo

- **Il caso**: premuto "Riavvia ora", il server e' tornato disponibile dopo
  sei secondi, ma la pagina e' rimasta in attesa fino a dare il riavvio per
  fallito.
- Ogni verifica ha ora un tempo limite proprio: una richiesta partita mentre
  il server stava chiudendo poteva restare appesa, bloccando tutti i
  tentativi successivi.
- La scadenza complessiva si misura sull'orologio invece che contando i
  cicli, cosi' una scheda lasciata in secondo piano non altera l'attesa; e
  tornando sulla scheda la verifica riparte subito.

### Il dialogo chiarisce che le impostazioni sono gia' salvate

- Dato che il dialogo compare al posto della normale conferma di
  salvataggio, non era ovvio se occorresse premere **Salva** una seconda
  volta. Ora il testo lo dice: le impostazioni sono gia' scritte, il riavvio
  serve solo ad applicarle.

## 9.3.4 - 2026-10-15

Diagnosi piu' facile quando qualcosa non va, e avvisi che compaiono solo
quando servono davvero.

### Nuovo: il server scrive un file di log

- Nell'eseguibile i messaggi del server non finivano da nessuna parte: la
  build non ha una console, quindi andavano persi, e il livello impostato
  tagliava via proprio le righe piu' utili (indirizzo del broker, metodo di
  connessione, interfaccia scelta). Davanti a un guasto non c'era nulla da
  leggere.
- Ora il server scrive in **`sardroid.log`**, accanto all'eseguibile, con
  rotazione automatica: al massimo quattro file da 2 MB. In sviluppo nulla
  cambia, li' la console c'e' gia'.

### Fix: il riavvio veniva richiesto anche quando non serviva

- Salvando una qualsiasi impostazione — per esempio la sola modalita' broker
  — compariva il dialogo "Riavvia il server per applicare la modifica", pur
  senza aver toccato i Servizi esposti. Il server considerava i bind
  "cambiati" ogni volta che arrivavano nella richiesta, e la pagina li invia
  sempre tutti.
- Separatamente, l'avviso giallo nella card confrontava il bind salvato con il
  valore predefinito invece che con quello realmente in ascolto: con un bind
  ristretto gia' attivo avrebbe continuato a chiedere un riavvio all'infinito.
- Entrambi corretti: ora si confronta con lo stato effettivo. Un avviso che
  compare sempre smette di essere letto proprio quando conta.

### Fix: il dialogo di riavvio poteva restare bloccato

- Premendo "Riavvia ora", se il server non tornava disponibile entro 40
  secondi il pulsante restava disabilitato su "Attendo il server" per sempre,
  senza messaggio e senza modo di riprovare: il timeout non solleva
  un'eccezione, quindi non veniva intercettato.
- Ora il pulsante torna attivo come **"Riprova"** e spiega cosa fare. Durante
  l'attesa mostra l'avanzamento, cosi' una pausa lunga non sembra un blocco.

### I QR mostrano l'indirizzo che useranno davvero

- Nelle card della mappa e dell'APK il selettore "Pubblica su" diceva
  "Predefinito (Rete)", come se il server scegliesse da solo. In realta' usa
  l'indirizzo della card "IP di default per i QR": se quello e' il residuo di
  un'altra rete, i QR non funzionano e li' non se ne vedeva traccia.
- Ora l'opzione mostra l'indirizzo effettivo e, se non corrisponde a nessuna
  scheda della macchina, lo segnala. Tradotto in italiano, inglese e spagnolo.


## 9.3.3 - 2026-10-14

Il broker integrato poteva non collegarsi mai perche' il client contattava
l'indirizzo sbagliato, e il messaggio d'errore non diceva cosa fare.

### Fix: con broker integrato il client contattava il broker esterno

- **Il caso**: modalita' *Integrato*, broker regolarmente in ascolto sulla
  1883, client che non si collega mai. Premendo **Start** nel tab Broker non
  cambiava nulla.
- **Causa**: quando l'indirizzo non viene passato esplicitamente, il client
  ricade su quello del broker **esterno** salvato nelle impostazioni. Due punti
  non lo passavano — il pulsante Start e la riconnessione automatica — quindi
  con un vecchio broker esterno rimasto in configurazione il client tentava
  quello (nel caso riscontrato `mqtt.flespi.io:443`, per giunta irraggiungibile
  dalla rete d'ufficio) mentre il broker integrato ascoltava sul computer
  stesso.
- **Fix**: in modalita' integrata indirizzo e porta sono ora sempre quelli del
  broker locale, qualunque sia la via da cui si avvia la connessione. Le
  impostazioni del broker esterno restano salvate e tornano valide appena si
  cambia modalita'.

### Fix: il messaggio d'errore cancellava la diagnosi

- Quando i tentativi di riconnessione si esaurivano, il server sostituiva
  l'eventuale spiegazione gia' scritta con "Riconnessione fallita dopo tutti i
  tentativi": una frase che dice cosa e' successo ma non cosa verificare, e
  che cancellava proprio l'informazione utile introdotta nella 9.3.2.
- Ora la diagnosi gia' presente viene mantenuta, e in sua assenza il messaggio
  indica almeno dove guardare.


## 9.3.2 - 2026-10-14

Il broker integrato poteva non connettersi mai, senza che nulla ne spiegasse
il motivo. Ora la causa viene rimossa da sola, e quando non basta viene detta.

### Fix: broker integrato che non si connette, in silenzio

- **Il caso**: modalita' *Integrato*, broker regolarmente in ascolto sulla
  1883, client che non si collega mai. La dashboard mostrava solo
  "Riconnessione automatica in corso" all'infinito, senza alcuna indicazione:
  per capirne la causa bisognava leggere la configurazione a mano.
- **Causa**: il client applicava anche in modalita' integrata i parametri
  `use_tls`, utente e password, che descrivono un broker **esterno**. Il
  broker integrato e' avviato dal server stesso in chiaro e anonimo, quindi
  l'handshake TLS non si completava mai. Bastava un flag rimasto acceso da una
  vecchia configurazione con broker esterno — e paho, in quel caso, ritenta
  senza riportare errori, quindi nessuno se ne accorgeva.
- **Rimedio automatico**: in modalita' integrata TLS e credenziali vengono ora
  ignorati, con una nota nel log. I valori restano salvati per quando si torna
  a un broker esterno, quindi non si perde nulla.

### Nuovo: il server dice perche' la connessione MQTT non riesce

- Dopo l'avvio il client verifica l'esito reale del collegamento: prima
  `connect_async()` ritornava subito e il server registrava "connesso" senza
  sapere se fosse vero. Un fallimento restava muto.
- Ora, se dopo pochi secondi il collegamento non c'e', viene indicata la causa
  piu' probabile, distinguendo i casi: porta che non risponde (broker spento o
  filtrato da firewall), TLS verso un broker che non lo parla, credenziali non
  accettate, oppure un servizio che non parla MQTT. Il messaggio compare nella
  dashboard sotto lo stato del broker.

### Fix: l'installer annunciava sempre la versione 9.1.5

- `installer.iss` conteneva un numero di versione fisso che sovrascriveva
  quello passato da `build_exe.bat`: in Inno Setup l'ultimo `#define` vince,
  quindi il parametro da riga di comando non aveva effetto e il numero e'
  rimasto indietro per decine di versioni.
- Ora la versione si legge dal file `VERSION`, la stessa fonte usata per
  l'eseguibile: non c'e' piu' nulla da allineare a mano. Se la compilazione
  passa comunque un valore esplicito, quello continua ad avere la precedenza.


## 9.3.1 - 2026-10-11

### Avviso: la chat non garantisce il recapito

- La tab **Messaggi** mostra ora un avviso fisso sopra l'elenco: i messaggi
  passano da MQTT senza conferma di consegna, quindi possono non arrivare, e
  sui dispositivi non vengono conservati. In emergenza questo va saputo prima,
  non scoperto quando un messaggio importante non e' stato letto.
- Stesso testo gia' presente nell'app mobile, cosi' l'operatore riceve la
  stessa indicazione da entrambe le parti. Tradotto in italiano, inglese e
  spagnolo.


## 9.3.0 - 2026-10-10

Il riavvio del server si fa ora dalla dashboard: niente piu' spegnimento e
rilancio a mano per applicare un cambio di rete.

### Nuovo: riavvio del server dalla dashboard

- Il dialogo che segnala un cambio di "Servizi esposti" ha ora un pulsante
  **"Riavvia ora"**: prima bisognava spegnere il server e rilanciare
  l'eseguibile a mano. La pagina attende che il server torni a rispondere e si
  ricarica da sola, cosi' l'esito e' visibile invece di lasciare un errore di
  connessione.
- Nuovo endpoint `POST /api/server/restart`, riservato a **localhost** come lo
  spegnimento: un riavvio fallito da remoto lascerebbe il sistema
  irraggiungibile. In sviluppo risponde con un messaggio esplicito invece di
  tentare un riavvio che non tornerebbe su.
- Lo script di riavvio attende che **tutti** i processi dell'eseguibile siano
  chiusi e che la porta sia libera. Con Nuitka onefile l'exe e' due processi
  (un bootstrap e quello reale): attendendo solo il secondo, il rilancio
  partiva mentre il primo era ancora vivo, e il nuovo processo trovava la
  porta occupata restando spento.
- Il rilancio passa `--no-browser`, come fa gia' l'aggiornamento automatico:
  la pagina che ha chiesto il riavvio si ricarica da sola, quindi aprirne
  un'altra lasciava due schede sulla stessa dashboard.
- Niente piu' richiesta di conferma del browser al ricaricamento: l'utente ha
  appena premuto "Riavvia ora" e le impostazioni sono gia' salvate.
- Lo script lascia una traccia in `restart.log` accanto all'eseguibile: si
  cancella da solo a fine corsa, quindi senza log un riavvio fallito non
  lasciava nulla da esaminare.

### Fix: le pause dell'aggiornamento automatico non attendevano

- Lo stesso difetto trovato nel riavvio valeva anche per
  [auto_updater.py](auto_updater.py): `timeout /t N` esce subito quando non
  c'e' una console interattiva, e lo script di update gira nascosto. I log lo
  confermano: fra "Attendo chiusura server" e "Server chiuso" passavano 0,18
  secondi invece di 4.
- Non ha mai causato danni perche' l'aggiornamento non si affida all'attesa:
  termina il processo con `taskkill /F` e verifica l'esito, con rollback in
  caso di errore. Ma fra la terminazione e la sostituzione dell'exe passavano
  10 millisecondi, e Windows puo' impiegare un istante a rilasciare gli handle
  del file: in quel caso l'aggiornamento sarebbe saltato senza una ragione
  reale. Le quattro pause ora attendono davvero.

## 9.2.9 - 2026-10-10

L'interruttore della mappa servita ha finalmente effetto.

### Fix: l'interruttore "Pubblica questa mappa" non aveva effetto

- **Bug**: spegnendo l'interruttore la mappa restava scaricabile dai
  dispositivi. Cambiava solo l'aspetto della pagina.
- **Causa**: la funzione che salva la configurazione scriveva sempre
  `enabled: true`, e il checkbox non aveva alcun gestore: il suo stato non
  arrivava mai al server. Il controllo lato download c'era gia' — mancava il
  collegamento.
- **Fix** in [static/settings.html](static/settings.html): l'interruttore
  salva al cambio e la selezione di una mappa rispetta la scelta fatta. In
  caso di errore torna allo stato precedente, invece di mostrarne uno falso.

## 9.2.8 - 2026-10-10

Un cambio alle impostazioni di rete poteva restare inattivo senza che nulla lo
dicesse: il server continuava a rifiutare i dispositivi pur essendo
configurato correttamente.

### Fix: nessun avviso che serviva riavviare

- **Il caso**: cambiando "Servizi esposti" il file viene scritto e la pagina
  mostra il valore nuovo, ma i bind sono letti **all'avvio**: il processo in
  esecuzione applica ancora quello precedente. Tutto sembra a posto e intanto i
  telefoni ricevono un rifiuto — il sintomo arriva dal campo, non dal server.
  L'unico avviso era un testo grigio in fondo alla card, generico e sempre
  presente, facile da non vedere.
- Al salvataggio compare ora un **dialogo di conferma**, mostrato solo quando i
  bind sono davvero cambiati: dice che la modifica non e' ancora attiva e cosa
  comporta finche' non si riavvia.
- Il badge in dashboard segnala il riavvio pendente anche a chi trova il server
  gia' in quello stato senza aver toccato nulla, confrontando il file con i
  valori in uso.

### Correzioni alla card "Routing rete"

- Il selettore segnalato in rosso apriva una tendina **bianca con il testo
  illeggibile**: lo sfondo semitrasparente veniva composto con il bianco di
  sistema. Ora e' opaco, come gli altri campi.
- L'avviso accomunava interfacce di **uscita** ed **entrata** in un messaggio
  solo, parlando sempre di "connessioni in uscita". Le conseguenze sono
  diverse — il server non si collega a nulla, oppure i servizi restano
  raggiungibili solo da questa macchina — e ora sono distinte.

## 9.2.7 - 2026-10-10

Un indirizzo di rete salvato che non esiste piu' bloccava il server senza
dirlo. Ora viene rilevato, aggirato e segnalato in dashboard. In piu', le
coordinate del cursore si leggono nelle tre notazioni usate sul campo.

### Fix: un IP di rete sparito bloccava tutte le connessioni, in silenzio

- **Bug**: con "Interfaccia di uscita" impostata su una scheda precisa, se
  quell'indirizzo cambiava (DHCP, rete diversa, cavo staccato) **nessuna
  connessione partiva piu'**: ne' il broker MQTT, ne' mappe, orario e
  aggiornamenti. I semafori restavano rossi senza alcuna indicazione, e in
  modalita' embedded sembrava che il broker non si avviasse.
- **Causa**: l'indirizzo veniva passato al `bind()` del socket (e a
  `local_address` per le richieste web) senza verificare che appartenesse
  ancora a una scheda locale. Un bind su un indirizzo inesistente fallisce e
  impedisce ogni uscita.
- **Fix** in [mqtt_handler.py](mqtt_handler.py) e [server.py](server.py): prima
  dell'uso si verifica che l'indirizzo esista. Se non c'e' piu', viene ignorato
  a favore dell'uscita automatica, con un avviso esplicito nel log. Il server
  continua a funzionare invece di restare muto.

### Nuovo: avviso in dashboard per la configurazione di rete

- Questi problemi erano visibili solo aprendo le impostazioni — cosa che in
  operazione non succede. Un badge rosso accanto ai semafori ora segnala le
  configurazioni non piu' valide, con il dettaglio al passaggio del mouse e il
  collegamento diretto al rimedio. Lo alimenta il nuovo endpoint
  `GET /api/network/health`, interrogato all'avvio e ogni due minuti, perche'
  la rete puo' cambiare a server acceso.

### Chiarita la card "IP di default per i QR"

- La descrizione spiegava il meccanismo, non la decisione. Ora dice che si
  tratta dell'indirizzo che **i telefoni** useranno, quando lasciare
  *Automatico* (una sola rete) e quando scegliere esplicitamente la scheda
  (piu' reti: va presa quella sulla stessa rete dei dispositivi).
- Il riquadro informativo ha ora quattro stati invece di due, e in particolare
  segnala in rosso un indirizzo **non presente su questa macchina**: i QR di
  pairing, APK e mappe punterebbero a un indirizzo irraggiungibile, cosa di cui
  ci si accorgeva solo sul campo. Non viene corretto d'ufficio, perche' dietro
  un NAT un indirizzo non locale e' legittimo.
- Con piu' schede attive e *Automatico*, un avviso ricorda che la scelta del
  sistema potrebbe non essere la rete dei telefoni.

### Nuovo: tre notazioni per le coordinate del cursore

- La barra in fondo alle mappe mostra una sigla (DD / DDM / DMS): un click
  passa alla notazione successiva, e la scelta si ricorda. Servono interlocutori
  diversi — gradi decimali per i sistemi, gradi e primi per aeronautica e
  marina, gradi primi secondi per la cartografia IGM. Mostrarle tutte e tre
  allungherebbe di tre volte una barra che insegue il cursore.
- La finestra **Ctrl+Click** le mostra invece tutte e tre insieme, ognuna
  copiabile con un click: li' si e' fermi e si sceglie. Sostituisce il bottone
  "Copia coordinate", che copiava solo in decimale.
- Corretto anche il passaggio del mouse sulla barra, che prima la faceva
  sfarfallare o sparire.

### Correzioni minori

- Tre testi dell'interfaccia comparivano come chiavi grezze
  (`settings.network.*`): traduzioni aggiunte in italiano, inglese e spagnolo.
- L'avviso sulle interfacce mancanti elencava due volte lo stesso indirizzo
  quando era usato da piu' campi.


## 9.2.6 - 2026-10-01

Distribuisce la nuova app Android inclusa nel server.

### Nuova APK Sardroid Mobile

- Aggiornata l'app Android distribuita insieme al server: si scarica dalla
  dashboard e viene estratta accanto all'eseguibile al primo avvio.
  Chi ha gia' l'app installata ricevera' la nuova versione dal server.

### Build: `net_proxy` incluso esplicitamente

- Il modulo di rilevamento proxy introdotto nella 9.2.4 era dichiarato in
  `build_nuitka.py`, che pero' **non** e' lo script usato dalla release:
  quella passa da [build_exe.bat](build_exe.bat). Il modulo veniva comunque
  seguito da Nuitka tramite gli import normali, ma al suo interno `winreg` e'
  importato in modo condizionale — lo stesso motivo per cui `socks` e'
  dichiarato esplicitamente. Ora `--include-module=net_proxy` e' anche in
  `build_exe.bat`.


## 9.2.5 - 2026-10-01

Dopo qualche minuto di sessione 3D tutti i segnaposto diventavano quadrati
neri. Era una perdita di memoria grafica causata dal lampeggio dei device.

### Fix: segnaposto 3D anneriti dopo alcuni minuti

- **Bug**: tenendo aperta la mappa 3D, dopo qualche minuto **tutti** i
  segnaposto — dispositivi, foto, waypoint — diventavano quadrati neri
  contemporaneamente. Ricaricando la pagina tornavano normali, per poi
  annerirsi di nuovo. Si manifestava soprattutto sulle dashboard aperte **da
  un altro PC**, raramente in locale.
- **Causa**: il timer di lampeggio dei dispositivi assegnava ogni **700 ms**
  un `<canvas>` nuovo a `billboard.image`. Cesium indicizza la cache delle
  texture sull'oggetto immagine, quindi ogni canvas era una texture nuova
  caricata sulla GPU e mai riutilizzata: circa 85 texture all'ora per
  dispositivo. Quando la memoria grafica si esauriva, le allocazioni
  successive fallivano e la scena rendeva nero tutto cio' che doveva
  ricaricare — anche foto e waypoint, che non lampeggiano affatto ed erano
  vittime, non causa. Da remoto il limite si raggiungeva prima, perche' quel
  PC spende memoria anche per terreno e tessere satellitari.
- **Fix** in [static/index.html](static/index.html) e
  [static/live-map.html](static/live-map.html): le icone dei dispositivi sono
  ora generate una sola volta per combinazione (colore x online x SOS x alone)
  e tenute in cache come dataURL. Il lampeggio riassegna sempre lo **stesso**
  oggetto, cosi' Cesium riconosce la texture e non ne carica una nuova. Nella
  simulazione, 600 chiamate del blink producono **4** immagini invece di 600.
- Allineate anche `createWaypointCanvas` e `createDeviceCanvas`, che
  restituivano un elemento canvas vivo invece di un dataURL: tutte le altre
  icone della scena gia' usavano dataURL.


## 9.2.4 - 2026-10-01

Il controllo aggiornamenti e il download dell'exe ora usano il proxy
configurato nel sistema: dietro una rete aziendale fallivano in silenzio.

### Fix: nessun aggiornamento rilevato dietro un proxy aziendale

- **Bug**: su una macchina dietro proxy, il controllo aggiornamenti restava
  appeso per 10 secondi e poi riportava "Timeout", senza che nessun
  aggiornamento risultasse mai disponibile — anche quando era pubblicato da
  giorni. Lo stesso valeva per il **download** dell'exe.
- **Causa**: `httpx` legge solo le variabili d'ambiente `HTTP_PROXY` /
  `HTTPS_PROXY`. Un exe lanciato dal menu Start non le ha, quindi tentava
  sempre l'uscita diretta, che in rete aziendale e' chiusa. Il proxy
  configurato in Windows non veniva mai considerato.
- **Fix**: nuovo modulo [net_proxy.py](net_proxy.py), usato da
  [update_checker.py](update_checker.py) e [auto_updater.py](auto_updater.py).
  Cerca il proxy in tre punti, dal piu' esplicito al piu' implicito:
  1. variabili d'ambiente (se presenti, comandano);
  2. proxy di sistema Windows, **solo** se `ProxyEnable = 1`;
  3. script PAC, **solo** se l'uscita diretta non funziona.

  Senza proxy configurato il comportamento resta identico a prima: chi esce
  in diretta continua a uscire in diretta.

### Nota: proxy con autenticazione

Se il proxy aziendale richiede credenziali, il controllo ora riporta un
errore esplicito (`407 Proxy Authentication Required`) invece di un timeout
muto. L'aggiornamento non viene ancora scaricato in quel caso: le credenziali
proxy gia' presenti nelle impostazioni MQTT non sono riusate dal
controllo aggiornamenti.


## 9.2.3 - 2026-10-01

Chiarisce il rifiuto del filtro anti-lockout: prima una restrizione di rete
era indistinguibile da una pagina mancante.

### Fix: il server rispondeva "404 non trovato" invece di dire che l'indirizzo non era autorizzato

- **Bug**: con "Servizi esposti" impostato su una rete specifica (es. la
  Ethernet), un telefono collegato al wifi che chiedeva la mappa riceveva un
  **404**. Sembrava un file mancante o un problema dell'app; in realta' era il
  bind che rifiutava la richiesta. La stessa cosa accadeva alla dashboard.
- **Causa**: il middleware `http_bind_filter` restituiva di proposito un 404
  muto, "per non rivelare la presenza del servizio a chi non e' autorizzato".
  In rete locale quella prudenza non protegge da nessuno e costa ore di
  diagnosi a chi non sospetta la restrizione.
- **Fix** in [server.py::http_bind_filter()](server.py): ora risponde **403**
  con un messaggio esplicito che indica l'indirizzo autorizzato, quello su cui
  e' arrivata la richiesta e dove cambiare l'impostazione. Le rotte `/api/`
  ricevono JSON (`detail`, `allowed_bind`, `requested_bind`), le altre testo
  semplice. Le due eccezioni anti-lockout restano intatte: loopback come
  destinazione e client da loopback passano sempre.

### UI: avviso prima di restringere i servizi esposti

- In [static/settings.html](static/settings.html), sotto "Servizi esposti", un
  avviso segnala che scegliendo una rete diversa da *Tutte le reti* dashboard,
  API e mappe diventano irraggiungibili dalle altre interfacce — cosi' la
  conseguenza si conosce prima di subirla, non dopo.


## 9.2.2 - 2026-10-01

Tre difetti che rendevano la configurazione poco affidabile: un flag TLS che
impediva di tornare a un broker in chiaro, i parametri di rete che sembravano
non salvarsi, e un pulsante "Salva" che non valeva per tutti i campi.

### Fix: impossibile disattivare il TLS dopo aver usato un broker che lo richiede

- **Bug**: configurando un broker **senza** TLS (es. `test.mosquitto.org:1883`)
  dopo averne usato uno con TLS, la connessione falliva sempre in timeout, senza
  alcun messaggio utile.
- **Causa**: il flag TLS esiste in due punti — `mqtt.external.use_tls` (tab
  Broker) e `mqtt.connection.use_tls` (tab Proxy) — e la regola era "basta che
  **uno dei due** sia attivo". Il secondo non veniva mai azzerato, quindi restava
  acceso per sempre: il server attivava il TLS su una porta in chiaro e il broker
  non rispondeva.
- **Fix** in [mqtt_handler.py::connect_client()](mqtt_handler.py): comanda il tab
  Broker, dove l'utente dichiara com'e' fatto il broker. L'altro flag vale solo
  come ripiego quando il primo non e' mai stato impostato.

### Fix: i parametri di rete sembravano non salvarsi

- **Bug**: modificando "Servizi esposti" (bind HTTP/MQTT) e salvando, riaprendo
  la pagina si ritrovava il valore precedente. Il salvataggio **funzionava** — il
  file veniva scritto — ma la UI mostrava altro.
- **Causa**: `GET /api/network/routing` restituiva `WEB_HOST` e
  `MQTT_BROKER_HOST`, costanti caricate **all'avvio del server**, invece di
  rileggere `server_config.ini`. La UI mostrava quindi il valore *attivo* al
  posto di quello *salvato*.
- **Fix** in [server.py](server.py): i bind inbound vengono riletti dal file. La
  risposta distingue ora `http_bind` (salvato, si applica al prossimo riavvio) da
  `http_bind_active` (in uso adesso), con un flag `restart_pending`.
- La pagina segnala in giallo quando i due differiscono: prima quella differenza
  era invisibile e sembrava un salvataggio non riuscito.

### Il pulsante "Salva" ora vale per tutta la pagina

Nelle Impostazioni convivevano tre comportamenti diversi — campi che
richiedevano il Salva, campi che si salvavano da soli, campi con un pulsante
proprio — senza che nulla lo segnalasse. Modificare un campo del secondo o terzo
gruppo e premere il Salva generale **perdeva la modifica in silenzio**.

- **4 campi di routing**: non erano registrati in `settingsConfig`, quindi il
  listener che accende il pulsante non si agganciava nemmeno. Ora lo fanno, e il
  salvataggio li instrada al loro endpoint dedicato (nuovo flag `skipBulk`:
  presenti nell'elenco per il rilevamento delle modifiche, esclusi dall'invio
  massivo).
- **7 permessi ospite**: si salvavano automaticamente al cambio, senza conferma.
  Ora seguono la regola comune.
- **Rimosso il pulsante "Salva routing"**: due comandi di salvataggio nella
  stessa pagina erano la causa principale dell'equivoco.
- **Campi della configurazione mappe**: lasciati invariati di proposito. Sono
  parametri di un'operazione (si compilano e si avvia il download), non
  impostazioni permanenti.
- **Nuovo avviso in uscita**: chiudere o ricaricare la pagina con modifiche non
  salvate chiede conferma.

### Fix: il bind di rete non veniva applicato ai test di connessione

- **Bug**: con due interfacce attive (es. Ethernet aziendale a metrica bassa e
  Wi-Fi libero), impostare `network.outbound_mqtt_ip` sul Wi-Fi non bastava:
  l'auto-detection provava la connessione **senza bind**, uscendo
  dall'interfaccia di default. Il test falliva, il server concludeva "nessun
  metodo di connessione disponibile" e non arrivava mai al punto in cui il bind
  viene applicato.
- **Fix**: l'impostazione viene letta **prima** dei test e passata a
  `_test_direct`; il bind e' stato inoltre esteso a tutti i rami di connessione
  (HTTP_CONNECT, SOCKS5 e i tre WebSocket), dove prima era usato solo da quello
  diretto.
- **Secondo difetto corretto nello stesso punto**: `_test_direct` usava
  `connect_ex` su un socket con timeout, che rende `10035` ("connessione
  avviata") invece di `0`. Il confronto con `0` falliva quindi **sempre**, anche
  a rete perfettamente funzionante. Sostituito con `connect()`.

### Nota per lo sviluppo

Avviando il server da una shell in cui sono impostate le variabili
`HTTPS_PROXY`/`HTTP_PROXY` (per esempio quella usata da Claude Proxy), PySocks le
applica a **tutti** i socket, incluso MQTT: il traffico viene instradato al proxy
e il bind sull'interfaccia scelta viene scavalcato. In sviluppo conviene avviare
il server con `start_dev.bat`, che non eredita quelle variabili.


## 9.2.1 - 2026-09-15

Rifiniture alla sincronizzazione zone introdotta con la 9.2.0.

### Riallineamento del broker all'avvio e al cambio sessione

- Lo stato dell'interruttore e' persistente nel database, ma il messaggio
  retained vive **sul broker**: i due potevano divergere. Il server ora
  **ripubblica le zone all'avvio** e **all'attivazione di una sessione**, se la
  distribuzione e' attiva.
- Copre due casi prima scoperti:
  - zone modificate a server spento (import diretto nel DB, ripristino di un
    backup): il broker restava con la versione precedente
  - broker che perde i retained (riavvio, cambio di token, pulizia): nessuno se
    ne accorgeva finche' non si toccava una zona
- Il **cambio sessione** era il caso piu' insidioso: il topic contiene il
  `session_id`, quindi i dispositivi si sottoscrivono a topic nuovi dove non
  c'e' ancora nulla. Senza ripubblicare sarebbero rimasti senza zone fino alla
  prima modifica.
- Entrambe le chiamate sono protette: se la pubblicazione fallisce il server
  parte comunque e lo segnala nel log.

### Fix: pallino sporgente sull'interruttore "Usa mappa offline"

- Difetto introdotto in questa stessa versione: lo stile dell'interruttore delle
  zone era stato definito su `.toggle-switch` **generico**, ma
  `.force-offline-toggle` usa la stessa classe `.toggle-slider` con un proprio
  cursore su `::after`. I due si sovrapponevano, e quello nuovo (18px contro
  10px) sporgeva dal contenitore da 28px.
- **Fix**: le regole sono state limitate a `#zonesDistLabel`, cosi' valgono solo
  per l'interruttore delle zone. Verificato con render di entrambi i componenti
  nei due stati.


## 9.2.0 - 2026-09-15

Le zone non si inviano piu' a comando: si accende un interruttore e i dispositivi
restano allineati da soli, compresi quelli che si accoppiano o tornano online in
seguito.

### Distribuzione automatica delle zone (messaggi retained)

- **Prima**: le zone si inviavano premendo "Invia ai Device". Un dispositivo che
  si accoppiava dopo, o che era spento in quel momento, **restava senza zone** e
  nessuno se ne accorgeva, perche' il server non sa chi ha ricevuto.
- **Ora**: le zone vengono pubblicate su MQTT con il flag **`retain`**. Il broker
  conserva l'ultimo messaggio e lo consegna a chiunque si sottoscriva in seguito:
  la consegna diventa responsabilita' del broker, e il server deve solo essere
  certo di aver pubblicato qualcosa di ricevibile.
- **Il pulsante e' diventato un interruttore** ("📡 Invia zone"):
  - **acceso** — le zone sono pubblicate e **ripubblicate a ogni modifica**
    (creazione, modifica, cancellazione, visibilita' singola e globale, e i
    quattro percorsi di import): dieci punti agganciati a un'unica funzione, cosi'
    non esistono percorsi che dimenticano di aggiornare i dispositivi
  - **spento** — viene pubblicata una **lista vuota**: le zone non sono piu'
    valide e i dispositivi le cancellano, restando allineati al server. Richiede
    conferma, perche' cancella dati su tutti i telefoni
- Le zone vengono pubblicate anche **al completamento del pairing**, sul topic del
  device appena accoppiato ([mqtt_handler.py](mqtt_handler.py), callback
  `set_on_device_paired`).
- Nuovi endpoint `GET`/`POST /api/zones/distribution`. `POST /api/zones/send`
  resta disponibile per invii a un singolo dispositivo e integrazioni esterne, ma
  non e' piu' usato dalla dashboard.

### Niente piu' messaggi inutili quando le zone non sono cambiate

- **Prima**: ogni invio produceva un messaggio in chat ("Zone aggiornate: N zone")
  anche quando il contenuto era identico a quello gia' presente sul dispositivo.
  Con i retained il problema si sarebbe aggravato, perche' il broker riconsegna il
  messaggio a ogni riconnessione.
- **Ora**: il payload `zones_sync` porta un campo **`hash`**, impronta del
  contenuto calcolata su zone ordinate per `id` (quindi insensibile a un
  riordino). L'app lo confronta con quello memorizzato: **avvisa solo se le zone
  sono davvero diverse**, e tace altrimenti.
- Il server **non invia piu' il messaggio di chat separato**: la decisione e'
  passata all'app, che e' l'unica a conoscere lo stato precedente.
- Lato app (`D:\sardroid`): nuova preferenza persistente `zonesHash`, confronto in
  `AppState`, e `MqttService.addLocalNotice()` per inserire l'avviso nella lista
  messaggi. Serve perche' la schermata Messaggi legge la lista MQTT in memoria e
  **non** il database, e il topic `/zones` ne e' escluso: era questa la ragione
  per cui il server doveva inviare un messaggio separato.
- Distinzione fra **lista vuota** (le zone non sono piu' valide: cancellare) e
  **nessun messaggio ricevuto** (il server non si e' espresso: non toccare nulla).

### Compatibilita'

Topic, cifratura e struttura delle zone sono invariati: l'unica aggiunta e' il
campo `hash`, che una versione precedente dell'app ignora continuando a funzionare
come prima. **Non e' necessario un rilascio coordinato** di server e app.

### Verifica sul campo

Collaudo end-to-end con telefono reale, server dietro proxy aziendale e broker
flespi in wss su 443. Verificati: ripubblicazione automatica alla modifica,
consegna al pairing, consegna alla riapertura dopo modifiche ad app chiusa,
cancellazione, e **assenza di notifica** quando le zone non sono cambiate.
Contratto e log in [docs/zone_sync_contratto.md](docs/zone_sync_contratto.md).


## 9.1.4 - 2026-09-06

Gli errori di connessione al broker ora sono visibili nell'interfaccia: prima un token non valido produceva un "non connesso" senza alcuna spiegazione.

### Fix: gli errori di connessione al broker non arrivavano all'interfaccia
- **Bug**: quando il broker **rifiutava** la connessione (token non valido, credenziali errate, client id rifiutato), `_on_connect` si limitava a un `logger.error` e **non popolava `connection_error`**. Nella dashboard si vedeva "non connesso" con il campo errore **vuoto**: nessun indizio sulla causa.
- Il difetto pesa soprattutto sull'**eseguibile distribuito**, dove non esiste un log su file: l'informazione andava semplicemente persa. Diagnosticare un token scaduto richiedeva di rilanciare l'exe da terminale per leggere i messaggi a schermo.
- **Fix** in [mqtt_handler.py::_on_connect()](mqtt_handler.py): il codice di ritorno viene tradotto in un messaggio leggibile e assegnato a `connection_error`, quindi mostrato nel pannello del broker — ad esempio *"Connessione al broker rifiutata: non autorizzato: verifica il token o le credenziali del broker"* per `reason_code = 5`.
- Caso reale che ha motivato il fix: un'installazione con un token flespi non valido restava "non connessa" senza spiegazione, pur avendo proxy, TLS e handshake WebSocket perfettamente funzionanti (`CONNECT 200` → `TLS 1.2` → `101 Switching Protocols`).

### Fix: diagnostica del broker con host errato
- **Bug**: in caso di connessione fallita la diagnostica riportava *"Broker localhost:1883 non raggiungibile"* anche con un broker esterno configurato, indicando un host che non c'entrava nulla.
- **Causa**: [server.py::_diagnose_broker_reachability()](server.py) leggeva `mqtt.external_host` e `mqtt.external_port` con l'**underscore**, chiavi che nessuno scrive; cadeva quindi sui valori di default. Le chiavi salvate dalla UI usano il **punto** (`mqtt.external.host`).
- **Fix**: allineate le due chiavi. La diagnostica ora nomina il broker realmente configurato.


## 9.1.3 - 2026-09-06

Il proxy MQTT non funzionava nell'eseguibile distribuito: difetto visibile solo in produzione, non in sviluppo.

### Fix: nell'exe compilato il proxy MQTT non funzionava (PySocks assente dal bundle)
- **Bug**: sull'eseguibile prodotto da Nuitka il server **non riusciva a connettersi al broker attraverso il proxy**, pur con una configurazione identica a quella funzionante in modalita' sviluppo. Il pannello restava su "non connesso" **senza alcun errore**.
- **Causa**: `build_nuitka.py` non includeva **PySocks**. Il modulo e' importato in modo condizionale in [mqtt_handler.py](mqtt_handler.py) (`try: import socks / except ImportError: SOCKS_AVAILABLE = False`), quindi Nuitka non lo rilevava automaticamente e l'import falliva **in silenzio** nell'exe. Con `SOCKS_AVAILABLE = False` la configurazione del proxy veniva saltata e il client tentava la **connessione diretta**: dietro un proxy aziendale resta appesa fino al timeout, senza motivo apparente.
- Il difetto era invisibile in sviluppo, dove PySocks e' installato nel venv: si manifestava **solo sull'eseguibile distribuito**, cioe' proprio in produzione.
- **Fix**: aggiunti `--include-module=socks` e `--include-module=sockshandler` a [build_nuitka.py](build_nuitka.py).
- **Fallimento reso esplicito**: se il proxy e' richiesto ma PySocks non e' disponibile, il client non tenta piu' una connessione diretta destinata a fallire, ma si ferma subito riportando *"PySocks non disponibile: impossibile usare il proxy... (build incompleta: manca il modulo 'socks')"*. Prima l'utente non aveva alcun indizio.


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
