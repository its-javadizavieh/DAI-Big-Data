# Lab 10 — Formati file: CSV, JSON, Parquet

## Obiettivo

Convertire dati tra CSV, JSON e Parquet; confrontare dimensioni e tempi di lettura; usare partitionBy.

## Durata (timebox)

30 minuti

## Prerequisiti

- Lab 06-09 completati
- Dataset CSV medio-grande (almeno 100.000 righe, scaricabile da Kaggle)

## Scenario

Il tuo team deve migrare un data lake da CSV a Parquet. Devi dimostrare i vantaggi con numeri concreti.

## Step

1. **Carica CSV** e mostra lo schema.
2. **Scrivi in JSON**:
   ```python
   df.write.json("output_json", mode="overwrite")
   ```
3. **Scrivi in Parquet**:
   ```python
   df.write.parquet("output_parquet", mode="overwrite")
   ```
4. **Confronta dimensioni**: usa il terminale o Python per misurare le dimensioni delle cartelle.

   ```python
   import os
   def folder_size(path):
       total = 0
       for f in os.listdir(path):
           fp = os.path.join(path, f)
           if os.path.isfile(fp):
               total += os.path.getsize(fp)
       return total / (1024*1024)  # MB

   print(f"JSON: {folder_size('output_json'):.1f} MB")
   print(f"Parquet: {folder_size('output_parquet'):.1f} MB")
   ```

5. **Confronta tempi di lettura**: leggi il CSV, il JSON e il Parquet con `time.time()` e confronta.
6. **Scrivi Parquet partizionato**:
   ```python
   df.write.partitionBy("category").parquet("output_partitioned", mode="overwrite")
   ```
7. **Leggi una singola partizione** e verifica che sia più veloce della lettura completa.

## Output atteso

- Tabella con: formato | dimensione MB | tempo lettura
- Screenshot della struttura cartelle del Parquet partizionato

## Checkpoint

- [ ] 3 formati scritti senza errori
- [ ] Parquet è significativamente più piccolo del CSV
- [ ] Il Parquet partizionato crea sottocartelle per categoria
- [ ] I tempi di lettura sono documentati

## Troubleshooting rapido

- "File already exists": usa `mode="overwrite"`
- "Output vuoto": verifica che il DataFrame di origine abbia righe (`df.count()`)

## Cleanup obbligatorio

```python
import shutil
for d in ["output_json", "output_parquet", "output_partitioned"]:
    shutil.rmtree(d, ignore_errors=True)
spark.stop()
```

## Parole chiave Google (screenshot/guide)

- "pyspark write parquet partitionBy example"
- "parquet vs csv performance comparison"
- "pyspark read json multiline"
