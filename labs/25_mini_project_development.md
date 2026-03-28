# Lab 25 - Mini-progetto: sviluppo e completamento

## Obiettivo

Completare il mini-progetto: query finali, salvataggio dei risultati, e sintesi conclusiva.

## Durata

30 minuti

## Prerequisiti

- Lab 24 completato
- Notebook già avviato

## Step

### Fase 1: Completa le query (10 minuti)

Scrivi almeno 2 query Spark SQL che rispondano alle tue domande principali.

```python
clean.createOrReplaceTempView("project_data")

q1 = spark.sql("""
SELECT ...
FROM project_data
...
""")

q2 = spark.sql("""
SELECT ...
FROM project_data
...
""")
```

### Fase 2: Salva i risultati (10 minuti)

```python
q1.write.mode("overwrite").parquet("output/query1.parquet")
q2.write.mode("overwrite").parquet("output/query2.parquet")

from tinydb import TinyDB

db = TinyDB("mini_project_results.json")
t1 = db.table("query1_results")
t2 = db.table("query2_results")

t1.truncate()
t2.truncate()

t1.insert_multiple([row.asDict() for row in q1.collect()])
t2.insert_multiple([row.asDict() for row in q2.collect()])
```

### Fase 3: Verifica (5 minuti)

```python
print(len(t1), len(t2))
print(t1.all()[:3])
print(t2.all()[:3])
```

### Fase 4: Scrivi il riepilogo finale (5 minuti)

Aggiungi una cella Markdown con:

- domande
- risposte brevi
- righe iniziali e righe pulite
- output salvati in Parquet e TinyDB
- conclusione finale

## Output atteso

- Query completate
- Output salvati
- TinyDB verificato
- Riepilogo finale scritto

## Checkpoint

- [ ] Almeno 2 query completate
- [ ] Parquet scritto
- [ ] TinyDB popolato
- [ ] Risultati verificati
- [ ] Conclusioni scritte
