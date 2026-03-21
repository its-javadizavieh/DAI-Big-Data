# Lab 01 — Introduzione ai Big Data e le 5V

## Obiettivo

Applicare il framework delle 5V a scenari della vita quotidiana e costruire un diagramma semplice del ciclo di vita dei dati.

## Durata (timebox)

30 minuti

## Prerequisiti

- Nessun software richiesto (carta + penna oppure un semplice editor di testo)
- Nessuna conoscenza di programmazione richiesta

## Scenario

Sei un analista dati per Spotify. Ogni giorno il sistema raccoglie:

- 600 milioni di utenti che ascoltano musica
- Ogni ascolto genera un record: canzone, artista, durata, timestamp, dispositivo
- Gli utenti creano playlist (testo libero per i nomi)
- Alcune registrazioni hanno il campo "durata" mancante o uguale a 0

Il tuo compito: analizzare questi dati per creare la playlist "Discover Weekly" personalizzata.

## Step

1. **Classifica le 5V**: per ognuna delle 5V, scrivi almeno 1 esempio specifico dal tuo scenario Spotify.

   | V        | Esempio da Spotify |
   | -------- | ------------------ |
   | Volume   | ?                  |
   | Velocity | ?                  |
   | Variety  | ?                  |
   | Veracity | ?                  |
   | Value    | ?                  |

   **Suggerimenti**:
   - Volume: quanti ascolti al giorno? quanti utenti?
   - Velocity: i dati arrivano in tempo reale o in batch?
   - Variety: che tipi di dati ci sono? (numeri, testo, audio...)
   - Veracity: ci sono dati mancanti o sbagliati?
   - Value: cosa si può ricavare da questi dati?

2. **Disegna il ciclo di vita dei dati**: usa questo schema e completa ogni fase con un esempio dallo scenario Spotify.

   ```
   Generazione → Raccolta → Archiviazione → Elaborazione → Analisi → Conservazione
   ```

   Per ogni fase scrivi:
   - Cosa succede?
   - Quale dato è coinvolto?

   Esempio per "Generazione": _Un utente preme play su una canzone → viene creato un record con canzone, artista, timestamp._

3. **Trova 3 problemi**: elenca almeno 3 sfide concrete dello scenario Spotify.

   Esempi di problemi da cercare:
   - Dati mancanti o errati
   - Troppi dati per un solo computer
   - Formati diversi da integrare

4. **Proponi soluzioni**: per ogni problema, suggerisci un approccio (non serve essere tecnici — basta il concetto).

   Esempio: _"I record con durata = 0 sono errori → soluzione: filtrarli durante l'elaborazione"_

## Output atteso

- Tabella 5V compilata con almeno 1 esempio per V
- Diagramma del ciclo di vita con annotazioni
- Lista di almeno 3 problemi con relative soluzioni

## Checkpoint

- [ ] Ogni V ha almeno 1 esempio concreto dallo scenario Spotify
- [ ] Il diagramma ha tutte le 6 fasi con annotazioni
- [ ] Almeno 3 problemi identificati con relative soluzioni

## Troubleshooting rapido

| Problema                          | Soluzione                                                            |
| --------------------------------- | -------------------------------------------------------------------- |
| Non so disegnare un diagramma     | Usa una lista con frecce → (es. "Generazione → Raccolta → ...")      |
| Non mi viene un esempio per una V | Pensa a un'altra app che usi (TikTok, Netflix, WhatsApp) e confronta |
| Non ho un editor                  | Carta e penna vanno benissimo per questo lab                         |

## Cleanup obbligatorio

- Nessun cleanup richiesto (lab teorico/cartaceo)

## Parole chiave Google (screenshot/guide)

- "5Vs of Big Data examples"
- "data lifecycle diagram simple"
- "spotify data how it works"
