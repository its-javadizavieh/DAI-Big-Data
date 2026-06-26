# Soluzioni Lab 11 — Introduzione a Spark Streaming

## Fase 1: Teoria — Batch vs Streaming

### Tabella compilata

| Caratteristica | Batch                        | Streaming                                    |
| -------------- | ---------------------------- | -------------------------------------------- |
| Dati elaborati | Tutti insieme, statici       | **In arrivo continuo, un pezzo alla volta**  |
| Frequenza      | Periodica (ogni ora, giorno) | **Continua (secondi/minuti)**                |
| Latenza        | Alta (minuti/ore)            | **Bassa (secondi)**                          |
| Esempio        | Report vendite mensili       | **Notifiche in tempo reale, dashboard live** |

### Risposte

**Cos'è un micro-batch?**
Un micro-batch è un piccolo gruppo di dati raccolti in una finestra temporale (es. ogni 1-5 secondi). Invece di processare ogni singolo evento individualmente, Spark raccoglie gli eventi per qualche secondo e poi li processa tutti insieme come un "mini-batch".

**Perché Spark usa il micro-batch?**
Perché è più efficiente: processare 1000 eventi insieme è molto più veloce che processare 1000 eventi uno alla volta. Il micro-batch offre un buon compromesso tra bassa latenza (pochi secondi) e alta efficienza (elaborazione in batch). Inoltre, Spark può riutilizzare lo stesso motore di ottimizzazione (Catalyst) usato per le query batch.

## Fase 2: Preparazione

```bash
mkdir -p streaming_input
mkdir -p streaming_output
```

**batch_01.csv:**

```csv
team,goals,competition
Juventus,2,Serie A
Milan,1,Serie A
Inter,3,Serie A
```

## Fase 3: Codice streaming

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, IntegerType

spark = SparkSession.builder.appName("Sol11_Streaming").getOrCreate()

schema = StructType([
    StructField("team", StringType(), True),
    StructField("goals", IntegerType(), True),
    StructField("competition", StringType(), True),
])

stream_df = (spark.readStream
    .option("header", True)
    .schema(schema)
    .csv("streaming_input/")
)

print(f"È uno stream? {stream_df.isStreaming}")
# Output: È uno stream? True

query = (stream_df.writeStream
    .outputMode("append")
    .format("console")
    .start()
)
```

**Output nella console (batch_01):**

```
-------------------------------------------
Batch: 0
-------------------------------------------
+---------+-----+-----------+
|     team|goals|competition|
+---------+-----+-----------+
| Juventus|    2|    Serie A|
|    Milan|    1|    Serie A|
|    Inter|    3|    Serie A|
+---------+-----+-----------+
```

## Fase 4: Nuovo file

**batch_02.csv** (creato MENTRE lo stream gira):

```csv
team,goals,competition
Napoli,4,Serie A
Roma,0,Coppa Italia
Lazio,2,Coppa Italia
```

**Output nella console (batch_02 — rilevato automaticamente):**

```
-------------------------------------------
Batch: 1
-------------------------------------------
+------+-----+------------+
|  team|goals| competition|
+------+-----+------------+
|Napoli|    4|     Serie A|
|  Roma|    0|Coppa Italia|
| Lazio|    2|Coppa Italia|
+------+-----+------------+
```

## Cleanup

```python
query.stop()
spark.stop()
```

```bash
rm -rf streaming_input/ streaming_output/
```