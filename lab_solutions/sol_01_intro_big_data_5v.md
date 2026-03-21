# Soluzioni Lab 01 — Introduzione ai Big Data e le 5V

## Esercizio 1: Tabella delle 5V (scenario Spotify)

| V            | Definizione               | Esempio concreto (Spotify)                                                                                    |
| ------------ | ------------------------- | ------------------------------------------------------------------------------------------------------------- |
| **Volume**   | Quantità di dati generati | 600 milioni di utenti × ~30 brani/giorno → miliardi di righe/giorno (~4 PB/mese)                              |
| **Velocity** | Velocità di generazione   | Ogni "play" genera un record in tempo reale; Discover Weekly aggiornata ogni lunedì (batch + real-time)       |
| **Variety**  | Diversità di formati      | Strutturati (tabella ascolti), semi-strutturati (metadati JSON artisti), non strutturati (file audio MP3/OGG) |
| **Veracity** | Qualità e affidabilità    | Ascolti con durata=0 (skip), duplicati (offline sync), metadati mancanti                                      |
| **Value**    | Informazioni utili        | Raccomandazioni personalizzate, report royalties, previsione trend musicali                                   |

## Esercizio 2: Ciclo di vita dei dati (Spotify)

| Fase                 | Cosa succede                        | Dati coinvolti   | Esempio                                                          |
| -------------------- | ----------------------------------- | ---------------- | ---------------------------------------------------------------- |
| **1. Generazione**   | I dati vengono creati               | Eventi grezzi    | Utente preme play → record (user_id, song_id, timestamp, device) |
| **2. Raccolta**      | I dati vengono trasmessi            | Stream di eventi | App invia evento ai server via API in tempo reale                |
| **3. Archiviazione** | I dati vengono salvati              | File/database    | Evento salvato in data lake (S3/HDFS) in formato Parquet         |
| **4. Elaborazione**  | I dati vengono trasformati          | Dati puliti      | Spark rimuove duplicati, aggiunge metadati (genere, artista)     |
| **5. Analisi**       | Si estraggono informazioni          | Report/modelli   | Algoritmo di raccomandazione calcola preferenze utente           |
| **6. Conservazione** | I dati vengono archiviati/eliminati | Dati storici     | Ascolti > 5 anni compressi e spostati in cold storage            |

```
Generazione → Raccolta → Archiviazione → Elaborazione → Analisi → Conservazione
   (play)      (API)      (data lake)      (Spark)       (ML)     (cold storage)
```

## Esercizio 3: 3 Problemi concreti

1. **Dati incompleti**: molti ascolti hanno durata = 0 secondi (skip). Conta come ascolto?
2. **Volume troppo grande**: 4 PB/mese → un singolo server non basta. Serve architettura distribuita.
3. **Formati diversi**: ascolti (CSV/Parquet), metadati artisti (JSON), audio (binario) → integrazione complessa.

## Esercizio 4: Soluzioni proposte

1. **Dati incompleti** → Regola: "un ascolto conta solo se durata ≥ 30 secondi". Filtro nella fase di elaborazione.
2. **Volume troppo grande** → Cluster di 100+ nodi Spark che elaborano in parallelo, ognuno su una partizione.
3. **Formati diversi** → Convertire tutto in formato unificato (Parquet) con schema comune. Audio resta separato, collegato tramite ID.
