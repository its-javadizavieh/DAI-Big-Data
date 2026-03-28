# Lab 24 - Mini-progetto: progettazione e avvio

## Obiettivo

Progettare una pipeline completa su uno dei dataset del corso e iniziare l'implementazione.

## Durata

30 minuti

## Prerequisiti

- Tutti i lab precedenti completati
- Dataset a scelta: `superstore_sales.csv` oppure `serie_a_coppa_italia_2015_2023.csv`

## Step

### Fase 1: Scegli dataset e tema (5 minuti)

Scrivi:

- dataset scelto
- tema scelto
- 3 domande di analisi semplici

### Fase 2: Compila il template (10 minuti)

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

### Fase 3: Avvia il notebook (15 minuti)

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("MiniProject").getOrCreate()
df = spark.read.csv("NOME_FILE.csv", header=True, inferSchema=True)
print(f"Righe: {df.count()}, Colonne: {len(df.columns)}")
```

Poi:

- seleziona le colonne più utili
- esegui `dropDuplicates()` e `dropna()`
- prepara almeno una prima query

## Output atteso

- Dataset scelto
- Template compilato
- Notebook avviato
- Pulizia iniziale fatta

## Checkpoint

- [ ] Tema scelto
- [ ] 3 domande scritte
- [ ] CSV caricato
- [ ] Pulizia iniziale eseguita
- [ ] Struttura del notebook pronta
