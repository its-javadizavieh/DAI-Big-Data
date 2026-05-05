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

Salva i risultati delle tue query in Parquet e TinyDB.

```python
# 1. Salva q1 e q2 in Parquet (mode="overwrite")
#    Percorsi: "output/query1.parquet", "output/query2.parquet"

# 2. Salva in TinyDB:
#    - Crea un database "mini_project_results.json"
#    - Crea 2 tabelle: "query1_results" e "query2_results"
#    - Svuota le tabelle e inserisci i risultati
#    Suggerimento: usa [row.asDict() for row in q1.collect()]

# Scrivi qui il tuo codice
```

### Fase 3: Verifica (5 minuti)

Stampa il conteggio documenti e i primi 3 risultati per ogni tabella TinyDB.

```python
# Scrivi qui il tuo codice di verifica
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
