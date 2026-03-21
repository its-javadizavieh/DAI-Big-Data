# Lab 11 - Introduzione a Spark Streaming + demo

## Obiettivo

Comprendere la differenza tra batch e streaming, e lanciare una demo di Structured Streaming che legge file CSV in arrivo.

## Durata

30 minuti

## Prerequisiti

- Lab 05-10 completati (PySpark DataFrame e Spark SQL)
- PySpark installato

## Nota

Questo lab mescola teoria (10 min) e demo pratica (20 min). La demo usa file CSV che copierete manualmente in una cartella per simulare dati "in arrivo".

---

## Step

### Fase 1: Teoria - Batch vs Streaming (10 minuti)

1. **Completa questa tabella:**

   | Caratteristica | Batch                        | Streaming |
   | -------------- | ---------------------------- | --------- |
   | Dati elaborati | Tutti insieme, statici       | \_\_\_\_  |
   | Frequenza      | Periodica (ogni ora, giorno) | \_\_\_\_  |
   | Latenza        | Alta (minuti/ore)            | \_\_\_\_  |
   | Esempio        | Report vendite mensili       | \_\_\_\_  |

2. **Rispondi:**
   - Cos'è un micro-batch? Come funziona in Spark Structured Streaming?
   - Perché Spark usa il micro-batch invece di processare evento per evento?

### Fase 2: Prepara la demo (5 minuti)

3. **Crea due cartelle nella tua directory di lavoro:**

   ```bash
   mkdir -p streaming_input
   mkdir -p streaming_output
   ```

4. **Crea il primo file CSV di test (simula dati in arrivo):**

   Salva come `streaming_input/batch_01.csv`:

   ```csv
   team,goals,competition
   Juventus,2,Serie A
   Milan,1,Serie A
   Inter,3,Serie A
   ```

### Fase 3: Lancia lo stream (10 minuti)

5. **Crea un notebook con questo codice:**

   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.types import StructType, StructField, StringType, IntegerType

   spark = SparkSession.builder.appName("Lab21_Streaming").getOrCreate()

   # Definisci lo schema (obbligatorio per streaming)
   schema = StructType([
       StructField("team", StringType(), True),
       StructField("goals", IntegerType(), True),
       StructField("competition", StringType(), True),
   ])
   ```

6. **Avvia la lettura dello stream:**

   ```python
   stream_df = (spark.readStream
       .option("header", True)
       .schema(schema)
       .csv("streaming_input/")
   )

   print(f"È uno stream? {stream_df.isStreaming}")
   ```

7. **Scrivi l'output (console):**

   ```python
   query = (stream_df.writeStream
       .outputMode("append")
       .format("console")
       .start()
   )
   ```

   Dovresti vedere il batch_01.csv processato nella console.

### Fase 4: Simula nuovi dati (5 minuti)

8. **SENZA fermare il notebook**, crea un nuovo file:

   Salva come `streaming_input/batch_02.csv`:

   ```csv
   team,goals,competition
   Napoli,4,Serie A
   Roma,0,Coppa Italia
   Lazio,2,Coppa Italia
   ```

   Spark lo rileverà automaticamente e lo processerà!

9. **Osserva** l'output nella console: il nuovo batch appare.

10. **Ferma lo stream:**

    ```python
    query.stop()
    spark.stop()
    ```

## Output atteso

- Tabella batch vs streaming compilata (Fase 1)
- Lo stream rileva `batch_01.csv` e lo processa
- Quando aggiungi `batch_02.csv`, viene processato automaticamente
- Lo stream si ferma correttamente con `query.stop()`

## Checkpoint

- [ ] Tabella batch vs streaming compilata
- [ ] Domanda su micro-batch risposta
- [ ] Cartelle `streaming_input/` e `streaming_output/` create
- [ ] Stream avviato e batch_01 processato
- [ ] batch_02 aggiunto e rilevato automaticamente
- [ ] Stream fermato correttamente

## Troubleshooting

| Problema                        | Soluzione                                                                |
| ------------------------------- | ------------------------------------------------------------------------ |
| "Schema must be specified"      | Lo schema è OBBLIGATORIO per readStream - definiscilo con StructType     |
| Nessun output                   | Aspetta 5-10 secondi. Lo stream controlla la cartella ogni pochi secondi |
| "Cannot call show on streaming" | Con streaming usa `writeStream`, NON `.show()`                           |
| Errore "already exists"         | Cancella `streaming_output/` e riprova                                   |

## Cleanup

```bash
rm -rf streaming_input/ streaming_output/
```
