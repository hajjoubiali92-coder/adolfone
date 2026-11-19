# 0 A.D. Android — controlli touch e multiplayer

## Supporto touch già presente nel sorgente Android.5

Il progetto contiene già il percorso completo degli eventi touch: `GameActivity.java`
legge gli eventi Android, `AndroidTouchInput.cpp` li inoltra al motore e
`TouchGestures.h` riconosce i gesti. Non serve aggiungere un overlay di pulsanti
fisso: i comandi contestuali e l'HUD originale restano utilizzabili sul display.

Controlli previsti:

- trascina un dito sul terreno: sposta la visuale;
- tocca un'unità o un edificio: seleziona e apre le azioni contestuali;
- trascina dopo una breve pressione: selezione a riquadro;
- doppio tocco su un'unità: seleziona unità simili visibili;
- pizzica: zoom;
- ruota due dita: rotazione della visuale;
- tocco breve con due dita: deseleziona;
- in modalità costruzione: tocco per posizionare l'anteprima, doppio tocco
  per confermare.

Il mouse e la tastiera fisici continuano a passare da SDL. Il tasto Indietro
Android invia ESC.

## Stato multiplayer / crossplay

Il sorgente attuale include ENet e le parti di rete necessarie al multiplayer,
ma la compilazione Android imposta `CONFIG2_LOBBY=0`. Il lobby online XMPP
(gloox) non viene compilato e il progetto non contiene la dipendenza gloox
nel lockfile/build Android. Perciò **non è corretto dichiarare il crossplay
online PC↔Android già funzionante**.

La presenza di ENet da sola non dimostra che una partita diretta sia completa:
servono anche la stessa versione/protocollo del motore, la configurazione di
sessione compatibile e un percorso di connessione raggiungibile. Il multiplayer
LAN/direct-IP è indicato dal progetto come compilato ma non testato su un
telefono; non è una certificazione di crossplay.

Per abilitare il vero crossplay online occorre portare e compilare gloox/XMPP,
riattivare il lobby, verificare TLS/certificati e compatibilità del servizio
lobby con la versione 0.28.0, poi testare una partita reale tra Android e PC.
Questa modifica non viene simulata cambiando semplicemente `CONFIG2_LOBBY`
a `1`: farlo senza la libreria e la configurazione corrette rischia di
rompere la compilazione o produrre un'app che non si connette.

## Verifiche eseguite su questo pacchetto sorgente

- Test gesti touch: 84 scenari superati, più 10.000 eventi misti.
- Test profilo prestazioni: 9 controlli superati.
- Test rendering/scene buffer: 14 controlli superati.
- Test frame pacing: 12 controlli superati.
- Test session-input: non eseguibile in questo ambiente perché manca
  `/var/tmp/0ad-android-build/assets/data/mods/public/public.zip`.
- Nessun test su dispositivo Android fisico e nessun test di partita online
  Android↔PC sono stati eseguiti.

Questi test verificano il codice e i gesti simulati; non sostituiscono una
compilazione completa, un test su telefono o una partita multiplayer reale.
