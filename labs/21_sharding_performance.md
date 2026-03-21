# Lab 21 - Sharding e ottimizzazione performance

## Obiettivo

Progettare una strategia di sharding per il dataset di vendite, ottimizzare query con indici, e migliorare aggregation pipeline.

## Durata

30 minuti

## Prerequisiti

- Lab 20 completato (indici MongoDB)
- MongoDB in esecuzione
- `pip install pymongo`
- Dataset `superstore_sales.csv` nella cartella di lavoro

## Dataset

**superstore_sales.csv** - 9.800 ordini. Caricheremo i dati in MongoDB per simulare un database di produzione.

---

## Step

### Fase 1: Progetta la shard key (5 minuti - carta/markdown)

1. **Analizza le colonne del dataset Superstore:**
   - `Order ID`, `Customer ID`, `Region`, `Category`, `Sales`, `Order Date`

2. **Rispondi a queste domande** (scrivi in markdown):
   - Quale colonna sceglieresti come shard key? Perché?
   - Useresti **range sharding** o **hash sharding**?
   - Quali sono i rischi della tua scelta?

   **Suggerimento:** una buona shard key ha:
   - Alta cardinalità (molti valori diversi)
   - Distribuzione uniforme
   - Query frequenti che la usano come filtro

### Fase 2: Carica i dati (5 minuti)

3. **Carica il CSV in MongoDB:**

   ```python
   import csv
   from pymongo import MongoClient

   client = MongoClient("mongodb://localhost:27017/")
   db = client["lab19_store"]
   col = db["orders"]
   col.delete_many({})

   with open("superstore_sales.csv", "r", encoding="utf-8") as f:
       reader = csv.DictReader(f)
       docs = []
       for row in reader:
           doc = {
               "order_id": row["Order ID"],
               "customer_id": row["Customer ID"],
               "customer_name": row["Customer Name"],
               "segment": row["Segment"],
               "city": row["City"],
               "state": row["State"],
               "region": row["Region"],
               "category": row["Category"],
               "sub_category": row["Sub-Category"],
               "sales": float(row["Sales"]),
           }
           docs.append(doc)

   col.insert_many(docs)
   print(f"Inseriti: {col.count_documents({})} documenti")
   client.close()
   ```

### Fase 3: Query senza indice (5 minuti)

4. **In `mongosh`, cerca ordini di un cliente:**

   ```javascript
   use lab19_store

   // SENZA indice
   db.orders.find({ customer_id: "CG-12520" }).explain("executionStats")
   ```

   **Annota:**
   - `totalDocsExamined`: \_\_\_\_
   - `executionTimeMillis`: \_\_\_\_

### Fase 4: Crea indice e confronta (5 minuti)

5. **Crea indice e ripeti:**

   ```javascript
   db.orders.createIndex({ customer_id: 1 });
   db.orders.find({ customer_id: "CG-12520" }).explain("executionStats");
   ```

   **Annota e confronta:**
   - `totalDocsExamined`: \_\_\_\_ (molto meno!)

### Fase 5: Aggregation pipeline - ottimizzazione (10 minuti)

6. **Pipeline LENTA (ordine sbagliato degli stage):**

   ```javascript
   var start = new Date();

   db.orders.aggregate([
     { $sort: { sales: -1 } },
     { $match: { region: "South" } },
     { $group: { _id: "$category", total: { $sum: "$sales" } } },
   ]);

   var end = new Date();
   print("Tempo LENTO: " + (end - start) + "ms");
   ```

7. **Pipeline VELOCE (metti $match prima!):**

   ```javascript
   var start = new Date();

   db.orders.aggregate([
     { $match: { region: "South" } },
     { $group: { _id: "$category", total: { $sum: "$sales" } } },
     { $sort: { total: -1 } },
   ]);

   var end = new Date();
   print("Tempo VELOCE: " + (end - start) + "ms");
   ```

   **Perché è più veloce?** Il `$match` all'inizio riduce subito il numero di documenti da processare.

8. **Compila la tabella di confronto:**

   | Scenario                  | Tempo (ms) |
   | ------------------------- | ---------- |
   | Pipeline con $match DOPO  | \_\_\_\_   |
   | Pipeline con $match PRIMA | \_\_\_\_   |

## Output atteso

- Shard key motivata (scritto in markdown)
- ~9.800 documenti in MongoDB
- Confronto performance con/senza indice
- Confronto pipeline lenta vs veloce

## Checkpoint

- [ ] Shard key scelta e motivata
- [ ] Dati caricati (~9.800 docs)
- [ ] Confronto explain() prima/dopo indice
- [ ] Pipeline lenta eseguita e cronometrata
- [ ] Pipeline veloce eseguita e cronometrata
- [ ] Regola d'oro memorizzata: **$match sempre prima!**

## Troubleshooting

| Problema                | Soluzione                                                                        |
| ----------------------- | -------------------------------------------------------------------------------- |
| Inserimento lento       | Usa `insert_many` in un'unica chiamata                                           |
| Tempi identici          | Su ~10K documenti la differenza è piccola - annotala comunque                    |
| Errore float conversion | Alcuni Sales potrebbero avere virgola. Usa `float(row["Sales"].replace(",",""))` |

## Cleanup

```javascript
use lab19_store
db.dropDatabase()
```
