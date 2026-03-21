# Lab 25 - Mini-progetto: sviluppo e completamento

## Obiettivo

Completare la pipeline del mini-progetto: analisi completa, salvataggio in MongoDB, e preparazione della presentazione.

## Durata

30 minuti

## Prerequisiti

- Lab 24 completato (progettazione e avvio)
- Notebook del Lab 24 aperto con dati già caricati

---

## Step

### Fase 1: Completa le query (10 minuti)

1. **Riprendi dal Lab 24.** Hai già risposto alla domanda 1.

2. **Query per la domanda 2:**

   ```python
   q2 = spark.sql("""
       SELECT ...
       FROM project_data
       ...
   """)
   print("Risposta domanda 2:")
   q2.show()
   ```

3. **Query per la domanda 3 (usa una tecnica avanzata come window function):**

   ```python
   q3 = spark.sql("""
       SELECT ...
       FROM project_data
       ...
   """)
   print("Risposta domanda 3:")
   q3.show()
   ```

### Fase 2: Salva in MongoDB (5 minuti)

4. **Salva i risultati principali:**

   ```python
   from pymongo import MongoClient

   client = MongoClient("mongodb://localhost:27017/")
   db = client["mini_project"]

   # Salva risultato query 1
   col1 = db["query_1_results"]
   col1.delete_many({})
   col1.insert_many([row.asDict() for row in q1.collect()])
   print(f"Query 1: {col1.count_documents({})} documenti salvati")

   # Salva risultato query 2
   col2 = db["query_2_results"]
   col2.delete_many({})
   col2.insert_many([row.asDict() for row in q2.collect()])
   print(f"Query 2: {col2.count_documents({})} documenti salvati")

   # Salva risultato query 3
   col3 = db["query_3_results"]
   col3.delete_many({})
   col3.insert_many([row.asDict() for row in q3.collect()])
   print(f"Query 3: {col3.count_documents({})} documenti salvati")

   client.close()
   ```

### Fase 3: Verifica in MongoDB (5 minuti)

5. **In `mongosh`:**

   ```javascript
   use mini_project
   show collections

   // Verifica un risultato
   db.query_1_results.findOne()
   db.query_1_results.countDocuments()
   ```

### Fase 4: Prepara la presentazione (10 minuti)

6. **Aggiungi una cella Markdown al notebook con il riepilogo:**

   ```markdown
   # Risultati Mini-Progetto

   ## Dataset: [nome]

   ## Tema: [titolo]

   ### Domanda 1: [domanda]

   **Risposta:** [1-2 frasi che riassumono il risultato]

   ### Domanda 2: [domanda]

   **Risposta:** [1-2 frasi]

   ### Domanda 3: [domanda]

   **Risposta:** [1-2 frasi]

   ### Pipeline utilizzata

   1. Ingestione: caricamento CSV → [N] righe
   2. Pulizia: dropna + dropDuplicates → [N] righe
   3. Analisi: 3 query Spark SQL (groupBy, window function, ...)
   4. Storage: 3 collezioni MongoDB con [N] documenti totali

   ### Conclusioni

   [2-3 frasi sulle scoperte principali]
   ```

7. **Salva il notebook** - è il tuo deliverable per la valutazione!

## Output atteso

- 3 query completate con risultati
- Risultati salvati in MongoDB (3 collezioni)
- Cella Markdown con riepilogo e conclusioni
- Notebook pronto per la presentazione

## Checkpoint

- [ ] Query 2 completata e funzionante
- [ ] Query 3 completata (con tecnica avanzata)
- [ ] Risultati salvati in MongoDB (3 collezioni)
- [ ] Verifica in mongosh OK
- [ ] Cella riepilogativa scritta con risposte
- [ ] Notebook salvato e pronto

## Valutazione

Il mini-progetto sarà valutato su:

| Criterio                                                | Peso |
| ------------------------------------------------------- | ---- |
| Pipeline funzionante (ingest → clean → analyse → store) | 40%  |
| Query corrette e risposta alle domande                  | 30%  |
| Documentazione e chiarezza del notebook                 | 20%  |
| Presentazione orale (Lab 27)                            | 10%  |

## Cleanup

```javascript
// Da fare solo DOPO la presentazione (Lab 27)!
use mini_project
db.dropDatabase()
```

```python
spark.stop()
```
