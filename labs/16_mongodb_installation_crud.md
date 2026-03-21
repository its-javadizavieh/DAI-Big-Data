# Lab 16 - MongoDB: installazione e operazioni CRUD

## Obiettivo

Installare MongoDB, connettersi con `mongosh`, e praticare le operazioni CRUD (Create, Read, Update, Delete) usando dati di partite di Serie A.

## Durata

30 minuti

## Prerequisiti

- Accesso a Internet per il download
- Permessi di installazione (oppure Docker installato)

## Scenario

Devi creare un piccolo database per archiviare le partite di Serie A. Imparerai a inserire, cercare, aggiornare e cancellare documenti.

## Dataset

Per questo lab inserirai i dati **manualmente** - così capisci come funziona MongoDB. Dal Lab 13 in poi userai i CSV del corso.

---

## Step

### Fase 1: Installazione (5 minuti)

1. **Installa MongoDB** - scegli UNA opzione:

   **Opzione A - Docker (consigliata):**

   ```bash
   docker run -d -p 27017:27017 --name mongo mongo:7
   ```

   **Opzione B - Installazione manuale:**
   - Linux: `sudo apt install mongodb-org`
   - Mac: `brew install mongodb-community`
   - Windows: scarica da mongodb.com/try/download

2. **Connettiti con mongosh:**
   ```bash
   mongosh
   ```
   Se vedi il prompt `test>`, sei connesso.

### Fase 2: Crea database e inserisci dati (10 minuti)

3. **Crea il database:**

   ```javascript
   use serie_a
   db.createCollection("partite")
   ```

4. **Inserisci 5 partite con `insertMany`:**
   ```javascript
   db.partite.insertMany([
     {
       home: "Juventus",
       away: "Roma",
       homeGoals: 3,
       awayGoals: 1,
       date: "2023-01-15",
       venue: "Allianz Stadium",
       city: "Torino",
       referee: "Daniele Orsato",
       season: 2023,
     },
     {
       home: "Milan",
       away: "Inter",
       homeGoals: 1,
       awayGoals: 2,
       date: "2023-02-05",
       venue: "San Siro",
       city: "Milano",
       referee: "Marco Guida",
       season: 2023,
     },
     {
       home: "Napoli",
       away: "Lazio",
       homeGoals: 2,
       awayGoals: 0,
       date: "2023-03-10",
       venue: "Diego Armando Maradona",
       city: "Napoli",
       referee: "Daniele Orsato",
       season: 2023,
     },
     {
       home: "Fiorentina",
       away: "Atalanta",
       homeGoals: 0,
       awayGoals: 0,
       date: "2022-11-20",
       venue: "Artemio Franchi",
       city: "Firenze",
       referee: "Luca Pairetto",
       season: 2023,
     },
     {
       home: "Torino",
       away: "Bologna",
       homeGoals: 4,
       awayGoals: 2,
       date: "2022-12-01",
       venue: "Olimpico Grande Torino",
       city: "Torino",
       referee: "Marco Guida",
       season: 2023,
     },
   ]);
   ```

### Fase 3: Cerca con `find` (8 minuti)

5. **Mostra tutte le partite:**

   ```javascript
   db.partite.find();
   ```

6. **Partite con più di 2 gol in casa:**

   ```javascript
   db.partite.find({ homeGoals: { $gt: 2 } });
   ```

7. **Partite arbitrate da Orsato:**

   ```javascript
   db.partite.find({ referee: "Daniele Orsato" });
   ```

8. **Partite giocate a Torino:**

   ```javascript
   db.partite.find({ city: "Torino" });
   ```

9. **Mostra solo squadre e risultato** (proiezione):
   ```javascript
   db.partite.find(
     {},
     { home: 1, away: 1, homeGoals: 1, awayGoals: 1, _id: 0 },
   );
   ```

### Fase 4: Aggiorna e cancella (7 minuti)

10. **Aggiorna una partita** - aggiungi il possesso palla:

    ```javascript
    db.partite.updateOne(
      { home: "Juventus", away: "Roma" },
      { $set: { possession_home: 0.55, possession_away: 0.45 } },
    );
    ```

11. **Aggiungi un campo a TUTTE le partite:**

    ```javascript
    db.partite.updateMany({}, { $set: { competition: "Serie A" } });
    ```

12. **Cancella una partita (lo 0-0):**

    ```javascript
    db.partite.deleteOne({ home: "Fiorentina", away: "Atalanta" });
    ```

13. **Conta i documenti rimasti:**
    ```javascript
    db.partite.countDocuments({});
    // Atteso: 4
    ```

## Output atteso

- MongoDB raggiungibile tramite `mongosh`
- 5 partite inserite con successo
- 4 query `find` con risultati corretti
- Update e delete eseguiti
- Conteggio finale: **4 documenti**

## Checkpoint

- [ ] `mongosh` si connette
- [ ] 5 documenti inseriti
- [ ] `find` con `$gt` funziona
- [ ] `find` con proiezione mostra solo campi scelti
- [ ] `updateOne` e `updateMany` completati
- [ ] `deleteOne` eseguita
- [ ] `countDocuments` ritorna 4

## Troubleshooting

| Problema                     | Soluzione                                      |
| ---------------------------- | ---------------------------------------------- |
| "Connection refused"         | Avvia MongoDB: `mongod` o `docker start mongo` |
| "Command not found: mongosh" | Installa: `npm install -g mongosh`             |
| "Duplicate key error"        | Fai prima `db.partite.drop()` e ripeti         |

## Cleanup

```javascript
use serie_a
db.dropDatabase()
```
