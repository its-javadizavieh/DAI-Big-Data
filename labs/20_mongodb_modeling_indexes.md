# Lab 20 — MongoDB: modellazione documenti e indici

## Obiettivo

Caricare dati reali di Serie A in MongoDB, progettare lo schema, creare indici e misurare i miglioramenti con `explain()`.

## Durata

30 minuti

## Prerequisiti

- Lab 16 completato (CRUD MongoDB)
- MongoDB in esecuzione
- `pip install pymongo`
- Dataset `serie_a_coppa_italia_2015_2023.csv` nella cartella di lavoro

## Dataset

**serie_a_coppa_italia_2015_2023.csv** — useremo le colonne principali per creare documenti MongoDB.

---

## Step

### Fase 1: Carica i dati in MongoDB (8 minuti)

1. **Carica il CSV in MongoDB con Python:**

   ```python
   import csv
   from pymongo import MongoClient

   client = MongoClient("mongodb://localhost:27017/")
   db = client["lab18_calcio"]
   col = db["partite"]

   # Pulisci la collezione se esiste
   col.delete_many({})

   # Leggi il CSV e inserisci solo le colonne principali
   with open("serie_a_coppa_italia_2015_2023.csv", "r", encoding="utf-8") as f:
       reader = csv.DictReader(f)
       docs = []
       for row in reader:
           doc = {
               "match_id": int(row["ID"]),
               "competition": row["Competition_Name"],
               "season": int(row["Season_End_Year"]),
               "date": row["Date"],
               "home": row["Home"],
               "away": row["Away"],
               "home_goals": int(row["HomeGoals"]) if row["HomeGoals"] else 0,
               "away_goals": int(row["AwayGoals"]) if row["AwayGoals"] else 0,
               "venue": row["Venue"],
               "city": row["venue_city"],
               "referee": row["Referee"],
           }
           docs.append(doc)

   col.insert_many(docs)
   print(f"Documenti inseriti: {col.count_documents({})}")
   ```

### Fase 2: Query senza indice (5 minuti)

2. **Apri `mongosh` e controlla i dati:**

   ```javascript
   use lab18_calcio
   db.partite.countDocuments()
   db.partite.findOne()
   ```

3. **Cerca partite della Juventus — misura le performance:**

   ```javascript
   db.partite.find({ home: "Juventus" }).explain("executionStats");
   ```

   **Scrivi questi numeri:**
   - `totalDocsExamined`: \_\_\_\_
   - `executionTimeMillis`: \_\_\_\_

### Fase 3: Crea indice e confronta (7 minuti)

4. **Crea un indice su `home`:**

   ```javascript
   db.partite.createIndex({ home: 1 });
   ```

5. **Riprova la stessa query:**

   ```javascript
   db.partite.find({ home: "Juventus" }).explain("executionStats");
   ```

   **Scrivi questi numeri e confronta:**
   - `totalDocsExamined`: \_\_\_\_ (dovrebbe essere MOLTO meno)
   - `executionTimeMillis`: \_\_\_\_

6. **Crea un indice composto** (home + season):

   ```javascript
   db.partite.createIndex({ home: 1, season: 1 });
   ```

7. **Testa con query che filtra entrambi:**

   ```javascript
   db.partite
     .find({
       home: "Juventus",
       season: 2023,
     })
     .explain("executionStats");
   ```

### Fase 4: Indice unico (5 minuti)

8. **Crea un indice unico su `match_id`:**

   ```javascript
   db.partite.createIndex({ match_id: 1 }, { unique: true });
   ```

9. **Prova a inserire un duplicato — cosa succede?**

   ```javascript
   db.partite.insertOne({ match_id: 8529, home: "Test", away: "Test" });
   // Atteso: errore "duplicate key"
   ```

### Fase 5: Compila la tabella di confronto

10. **Compila questa tabella nei tuoi appunti:**

    | Query                              | docsExamined SENZA indice | docsExamined CON indice | Miglioramento |
    | ---------------------------------- | ------------------------- | ----------------------- | ------------- |
    | find home="Juventus"               | \_\_\_                    | \_\_\_                  | \_\_\_x       |
    | find home="Juventus" + season=2023 | \_\_\_                    | \_\_\_                  | \_\_\_x       |

## Output atteso

- ~3.788 documenti in MongoDB
- Confronto performance prima/dopo indice
- Errore "duplicate key" per indice unico
- Tabella compilata

## Checkpoint

- [ ] CSV caricato in MongoDB (~3.788 docs)
- [ ] `explain()` senza indice mostra totalDocsExamined alto
- [ ] Dopo indice, docsExamined diminuisce molto
- [ ] Indice composto funziona
- [ ] Indice unico previene duplicati
- [ ] Tabella confronto compilata

## Troubleshooting

| Problema                     | Soluzione                                                                        |
| ---------------------------- | -------------------------------------------------------------------------------- |
| "explain() non mostra stats" | Usa `"executionStats"` come argomento                                            |
| Indice non migliora          | Su ~3.800 righe la differenza può essere piccola, ma docsExamined DEVE diminuire |
| Errore encoding CSV          | Aggiungi `encoding="utf-8"` nel `open()`                                         |

## Cleanup

```javascript
use lab18_calcio
db.dropDatabase()
```
