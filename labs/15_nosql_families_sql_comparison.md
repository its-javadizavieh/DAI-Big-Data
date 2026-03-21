# Lab 15 — Famiglie NoSQL e confronto con SQL

## Obiettivo

Classificare scenari reali per tipo di database ideale, progettare schemi documentali e confrontare approcci SQL vs NoSQL.

## Durata (timebox)

30 minuti

## Prerequisiti

- Conoscenza base di SQL (SELECT, JOIN, CREATE TABLE)
- Slide della lezione 15

## Scenario

Sei consulente IT e devi consigliare al cliente quale database usare per 4 servizi diversi.

## Step

1. **Classifica i servizi**: per ognuno dei seguenti scenari, indica il tipo di database ideale (SQL, Document, KV, Column-family, Graph) e motiva la scelta.
   - a) Sistema bancario per trasferimenti di denaro
   - b) Social network con relazioni amici-di-amici
   - c) Catalogo e-commerce con prodotti eterogenei
   - d) Cache per sessioni utente con TTL (Time-To-Live)
   - e) Dati IoT da 10.000 sensori con letture ogni secondo

2. **Progetta uno schema documentale**: per il catalogo e-commerce (scenario c), crea 3 documenti JSON di esempio per prodotti di categorie diverse (elettronica, abbigliamento, alimentari).

3. **Confronto SQL vs NoSQL**: per lo scenario c, scrivi come modelleresti lo stesso dato in SQL (tabelle, relazioni) e in MongoDB (documenti). Evidenzia pro e contro di ciascun approccio.

4. **Polyglot persistence**: disegna un'architettura che usa almeno 3 tipi di database diversi per un'applicazione e-commerce completa.

## Output atteso

- Tabella classificazione: servizio | tipo DB | motivazione
- 3 documenti JSON di esempio
- Confronto SQL/NoSQL per lo scenario e-commerce
- Schema architetturale polyglot persistence

## Checkpoint

- [ ] Tutti e 5 i servizi classificati con motivazione
- [ ] I 3 documenti JSON sono validi e realistici
- [ ] Il confronto SQL/NoSQL ha almeno 3 pro e 3 contro per lato
- [ ] L'architettura polyglot usa almeno 3 database diversi

## Troubleshooting rapido

- Se non sai quale database scegliere: chiediti "Serve scalabilità o consistenza? Schema fisso o flessibile?"
- Se il JSON non è valido: usa un validator online (jsonlint.com)

## Cleanup obbligatorio

- Nessun cleanup richiesto (lab teorico/progettuale)

## Parole chiave Google (screenshot/guide)

- "nosql database types comparison chart"
- "mongodb document schema design"
- "polyglot persistence architecture example"
