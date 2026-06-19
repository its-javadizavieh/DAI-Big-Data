# Lab 23 - Mini-progetto: design, sviluppo e consegna

## Obiettivo

Progettare e completare un mini-progetto end-to-end su uno dei dataset del corso.

## Durata

2 ore

## Prerequisiti

- Tutti i lab precedenti completati
- Dataset a scelta: `superstore_sales.csv` oppure `serie_a_coppa_italia_2015_2023.csv`

## Step

### Fase 1: Scegli dataset e tema

Scrivi:

- dataset scelto
- tema scelto
- 3 domande di analisi semplici

Compila il template:

```markdown
# Mini-Progetto: [Titolo]

## Dataset

- Nome file: ...
- Colonne principali: ...

## Domande

1. ...
2. ...
3. ...

## Pipeline prevista

1. Ingestione con PySpark
2. Pulizia
3. Analisi con Spark SQL
4. Salvataggio in Parquet e TinyDB
5. Presentazione risultati
```

### Fase 2: Ingest e pulizia

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("MiniProject").getOrCreate()
df = spark.read.csv("NOME_FILE.csv", header=True, inferSchema=True)
print(f"Righe: {df.count()}, Colonne: {len(df.columns)}")
df.printSchema()
df.show(5, truncate=False)

clean = df.dropDuplicates().dropna()
print(f"Righe pulite: {clean.count()}")
```

### Fase 3: Completa le query

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

### Fase 4: Salva i risultati

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

### Fase 5: Verifica e riepilogo

Stampa il conteggio documenti e i primi 3 risultati per ogni tabella TinyDB.

Aggiungi una cella Markdown con:

- domande
- risposte brevi
- righe iniziali e righe pulite
- output salvati in Parquet e TinyDB
- conclusione finale

## Output atteso

- Dataset scelto e template compilato
- Notebook completo end-to-end
- Query completate e output salvati
- TinyDB verificato
- Riepilogo finale scritto

## Checkpoint

- [ ] Tema e 3 domande scritte
- [ ] CSV caricato e pulito
- [ ] Almeno 2 query completate
- [ ] Parquet scritto
- [ ] TinyDB popolato
- [ ] Risultati verificati
- [ ] Conclusioni scritte