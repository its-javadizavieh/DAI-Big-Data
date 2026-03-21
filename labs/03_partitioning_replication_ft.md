# Lab 03 — Partizionamento, replicazione, fault tolerance

## Obiettivo

Scegliere come dividere i dati, capire perché fare copie, e simulare cosa succede quando un computer si rompe.

## Durata (timebox)

30 minuti

## Prerequisiti

- Lab 02 completato (conoscenza architettura cluster: boss e helper)

## Scenario

Gestisci i dati di una piattaforma di streaming musicale (tipo Spotify) con un cluster di 4 helper. Hai 3 dataset:

- **Dataset A**: cronologia ascolti di tutti gli utenti italiani — 8 GB
  - Colonne: `user_id, canzone, artista, timestamp, durata_secondi`
- **Dataset B**: anagrafica utenti — 1 GB
  - Colonne: `user_id, nome, eta, citta, data_iscrizione`
- **Dataset C**: catalogo canzoni — 2 GB
  - Colonne: `canzone_id, titolo, artista, genere, anno, durata`

## Step

1. **Scegli come dividere ogni dataset**: per ciascun dataset, scegli una strategia di partizionamento e spiega perché.

   Le opzioni sono:
   - **Per range**: dividere per intervalli (es. utenti A-F su Helper 1, G-L su Helper 2...)
   - **Per hash**: applicare una formula matematica all'ID → distribuzione uniforme
   - **Round-robin**: riga 1 → Helper 1, riga 2 → Helper 2, riga 3 → Helper 3, e ricomincia

   | Dataset           | Strategia | Perché? |
   | ----------------- | --------- | ------- |
   | A (ascolti, 8 GB) | ?         | ?       |
   | B (utenti, 1 GB)  | ?         | ?       |
   | C (canzoni, 2 GB) | ?         | ?       |

   **Suggerimento**: pensa a come farai le query. Se cerchi "tutti gli ascolti di un utente", conviene che siano sullo stesso helper.

2. **Calcola lo spazio con le copie (replicazione)**: se vuoi 3 copie di ogni dato (per sicurezza), quanto spazio totale serve?

   ```
   Dataset A: 8 GB × 3 copie = ? GB
   Dataset B: 1 GB × 3 copie = ? GB
   Dataset C: 2 GB × 3 copie = ? GB
   Totale: ? GB
   ```

3. **Trova il problema dello skew**: quale dataset è più a rischio di distribuzione non uniforme?

   Esempio: se partizioni il Dataset B per città, Roma avrà molti più utenti di Aosta.
   - Quale dataset e quale campo hanno questo rischio?
   - Perché è un problema? (cosa succede all'helper che riceve troppi dati?)
   - Come risolveresti? (suggerimento: considera hash partitioning)

4. **Simula un guasto**: l'Helper 2 si spegne. Conteneva una partizione del Dataset A e una del Dataset C.

   Descrivi in 4 passi cosa succede:

   ```
   Passo 1: Il boss nota che l'Helper 2 non manda più heartbeat → ...
   Passo 2: Il boss controlla dove sono le copie dei dati dell'Helper 2 → ...
   Passo 3: Il boss ri-assegna i compiti dell'Helper 2 → ...
   Passo 4: Il lavoro continua → ...
   ```

## Output atteso

- Tabella con: dataset | strategia | motivazione
- Calcolo dello spazio totale con replicazione
- Identificazione dello skew con soluzione proposta
- Descrizione del recovery in 4 passi

## Checkpoint

- [ ] Ogni dataset ha una strategia giustificata
- [ ] Calcolo replicazione corretto (11 GB × 3 = 33 GB totali)
- [ ] Lo skew è identificato con un esempio concreto
- [ ] Recovery descritto in 4 passi sequenziali

## Troubleshooting rapido

| Problema                          | Soluzione                                                                                                         |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Non so scegliere la strategia     | Chiediti: "Come cercherò i dati?" — se per chiave specifica → hash; se per range di date → range                  |
| Il calcolo replicazione non torna | Spazio totale = dimensione × numero di copie                                                                      |
| Non capisco lo skew               | Pensa: se dividi una classe per cognome iniziale, la lettera "R" (Rossi, Russo...) avrà molti più studenti di "Q" |

## Cleanup obbligatorio

- Nessun cleanup richiesto (lab teorico)

## Parole chiave Google (screenshot/guide)

- "data partitioning hash vs range simple"
- "replication factor explained"
- "partition skew problem and solution"
