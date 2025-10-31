Analisi Dati Musicali: Correlazione tra Features Audio e Successo Commerciale
Questo progetto è un'analisi esplorativa dei dati (EDA) che indaga la relazione tra le caratteristiche audio intrinseche di una canzone (come la "ballabilità" o l'"energia") e il suo successo commerciale (misurato in stream e visualizzazioni).

L'obiettivo principale è rispondere alla domanda: "Esiste una 'formula' per una hit? Le canzoni di successo condividono un profilo audio simile?"

La Fonte Dati: Un Pivot dall'API a un CSV Locale
Inizialmente, il mio obiettivo era utilizzare l'API ufficiale di Spotify per estrarre dati in tempo reale sulle classifiche "Top 50".

Tuttavia, durante lo sviluppo, ho incontrato un blocco insormontabile. Nonostante l'autenticazione (Client ID/Secret) fosse valida (confermata da chiamate API di base come sp.artist()), qualsiasi richiesta di dati di playlist (come sp.playlist_tracks()) falliva costantemente con un errore 404 (Resource Not Found).

Dopo un lungo processo di debug (che ha escluso problemi di rete locale, firewall, antivirus, l'uso di Google Colab e persino la creazione di nuove chiavi API e account), ho stabilito che il problema risiede nella "Development Mode" dell'app sul portale Spotify Developer. L'app rimane in una "sandbox" (recinto di sicurezza) e l'invito via email per sbloccarla (necessario per aggiungere utenti alla "allowlist") è risultato buggato e non arriva mai.

Per superare questo blocco e procedere con l'analisi, ho cambiato strategia. Ho deciso di utilizzare un dataset statico (un file CSV) trovato su Kaggle. Questo file (Spotify_Youtube_Dataset.csv) si è rivelato una buona fonte dati, poiché contiene già tutte le features audio di Spotify e le metriche di successo di YouTube, permettendomi di eseguire l'analisi ibrida che volevo.

Struttura del Notebook e Analisi Eseguite
Il mio notebook Jupyter è diviso in passaggi logici. Ogni cella contiene commenti che spiegano cosa fa ogni riga di codice.

1. Caricamento e Pulizia dei Dati
Il primo passaggio è stato caricare il file .csv in un DataFrame pandas. Il passaggio più importante è stato la pulizia:

Ho rinominato tutte le colonne in minuscolo (es. da Danceability a danceability) per standardizzarle.

Ho rimosso le righe con dati mancanti (dropna) nelle colonne fondamentali (come stream, views, o le features audio) per garantire l'integrità dell'analisi.

2. Glossario: Features vs. Metriche
Ho inserito una cella per spiegare i dati che sto analizzando.

Features (Caratteristiche Audio): Sono le proprietà intrinseche della canzone (come suona). Sono metriche proprietarie di Spotify (originate da The Echo Nest), come danceability, energy (intensità) e valence (positività/umore).

Metriche (Metriche di Successo): Sono i numeri che misurano la popolarità, come stream (Spotify), views (YouTube) e likes.

3. Analisi 1: Profilo Audio e Correlazione Iniziale
Ho creato due grafici per una prima comprensione.

Grafico 1 (Barre): Profilo Audio Medio. Ho calcolato la media delle features artistiche (scala 0-1).

Interpretazione: Dal grafico noto che il mio dataset è sbilanciato verso canzoni ad alta energy e danceability, e molto basse in acousticness. Questo suggerisce un dataset focalizzato sulla musica pop ed elettronica, piuttosto che acustica.

Grafico 2 (Scatter): Danceability vs. Views. Ho confrontato la ballabilità con le visualizzazioni. Ho usato una scala logaritmica (log_y=True), un passaggio fondamentale per gestire le canzoni "superstar" (outlier) e rendere il grafico leggibile.

Interpretazione: Il grafico mostra una "nuvola" di punti sparsa. Questo indica che non c'è correlazione tra la ballabilità e il successo su YouTube. Il successo dipende da altri fattori.

4. Analisi 2: Matrice di Correlazione (Heatmap)
Per avere una visione d'insieme di tutte le relazioni, ho generato una "heatmap" di correlazione.

Interpretazione: La heatmap mi ha confermato visivamente che danceability e valence (umore) hanno correlazioni vicine allo zero con views e stream. Le uniche correlazioni positive degne di nota, sebbene deboli, sono tra loudness (volume) ed energy e le metriche di successo.

5. Analisi 3: Confronto "Hit vs. Flop"
Ho definito le "Hit" (Top 10% per views) e i "Flop" (Bottom 10%) e ho confrontato il loro profilo audio medio.

Interpretazione: Questo grafico a barre raggruppate è stato molto chiaro. Ha rivelato che danceability e valence sono quasi identici tra Hit e Flop. La vera differenza è nell'acousticness: le canzoni "Hit" sono significativamente meno acustiche dei "Flop", rafforzando l'ipotesi che la musica molto prodotta ed elettronica domini questo dataset.

6. Analisi 4: Classifiche Top 10
Ho estratto le classifiche assolute basate sugli stream di Spotify per identificare i "pesi massimi" nel dataset.

Ho creato una tabella delle "Top 10 Canzoni" (con le loro features).

Ho creato una tabella e un grafico a barre dei "Top 10 Artisti" (basati sulla somma di tutti i loro stream).

7. Analisi 5: "Artist Wrapped" (Dashboard Interattiva)
Come analisi finale, ho costruito uno strumento di esplorazione interattivo con ipywidgets.

Ho creato un menu a tendina (Dropdown) con la lista di tutti gli artisti.

Ho collegato il widget a una funzione Python (on_artist_change) che genera un "Wrapped" al volo ogni volta che si seleziona un nuovo artista.

Interpretazione: Questa dashboard è lo strumento di esplorazione finale. Mi permette di selezionare rapidamente artisti diversi e confrontare i loro profili audio medi e le loro canzoni top sia su Spotify che su YouTube.