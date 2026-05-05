# Lab 18 - Pulizia dati e trasformazioni batch

## Obiettivo

Pulire un dataset reale (partite di Serie A) gestendo null, duplicati, tipi errati e valori anomali.

## Durata

30 minuti

## Prerequisiti

- Lab 06-10 completati (DataFrame API, Spark SQL)
- Dataset `serie_a_coppa_italia_2015_2023.csv` nella cartella di lavoro

## Dataset

**serie_a_coppa_italia_2015_2023.csv** - 3.788 partite, ~230 colonne. Per questo lab ci concentreremo sulle colonne principali: `ID`, `Competition_Name`, `Season_End_Year`, `Date`, `Home`, `Away`, `HomeGoals`, `AwayGoals`, `Referee`, `possessiontime_home`, `possessiontime_away`, `shots_total_home`, `shots_ongoal_home`, `yellow_cards_home`, `red_cards_home`, `fouls_home`.

---

## Step

### Fase 1: Carica e ispeziona (5 minuti)

1. **Carica il CSV e seleziona le colonne principali:**

   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.functions import col, count, when, isnan, trim, to_date

   spark = SparkSession.builder.appName("Lab16_Cleaning").getOrCreate()

   raw_df = spark.read.csv("serie_a_coppa_italia_2015_2023.csv",
                           header=True, inferSchema=True)
   print(f"Righe totali: {raw_df.count()}")
   print(f"Colonne totali: {len(raw_df.columns)}")
   ```

2. **Seleziona solo le colonne che ci servono:**

   ```python
   cols = ["ID", "Competition_Name", "Season_End_Year", "Date",
           "Home", "Away", "HomeGoals", "AwayGoals", "Referee",
           "possessiontime_home", "possessiontime_away",
           "shots_total_home", "shots_ongoal_home",
           "yellow_cards_home", "red_cards_home", "fouls_home"]

   df = raw_df.select(cols)
   df.printSchema()
   df.show(5)
   ```

### Fase 2: Conta i null (5 minuti)

3. **Conta i null per ogni colonna:**

   ```python
   null_counts = df.select([
       count(when(col(c).isNull(), c)).alias(c)
       for c in df.columns
   ])
   print("Null per colonna:")
   null_counts.show()
   ```

   Annota quali colonne hanno più null.

### Fase 3: Gestisci i null (7 minuti)

4. **Colonne critiche - rimuovi righe senza ID o squadre:**

   Usa `dropna` per rimuovere le righe dove mancano `ID`, `Home` o `Away`.

   ```python
   step1 = df.dropna(subset=[___])
   print(f"Dopo dropna critici: {step1.count()} righe")
   ```

5. **Colonne numeriche - riempi con 0 dove manca:**

   Usa `fillna` per riempire con 0 le colonne: `shots_total_home`, `shots_ongoal_home`, `yellow_cards_home`, `red_cards_home`, `fouls_home`.

   ```python
   numeric_cols = ["shots_total_home", "shots_ongoal_home",
                   "yellow_cards_home", "red_cards_home", "fouls_home"]

   step2 = step1.fillna(___, subset=___)
   print(f"Dopo fillna numerici: {step2.count()} righe")
   ```

6. **Colonne testo - riempi con "Sconosciuto":**

   Usa `fillna` per riempire con `"Sconosciuto"` la colonna `Referee`.

   ```python
   step3 = step2.fillna(___, subset=[___])
   print(f"Dopo fillna testo: {step3.count()} righe")
   ```

### Fase 4: Rimuovi duplicati (3 minuti)

7. **Controlla e rimuovi duplicati:**

   Rimuovi i duplicati basandoti sulla colonna `ID`.

   ```python
   before = step3.count()
   step4 = step3.dropDuplicates([___])
   after = step4.count()
   print(f"Duplicati rimossi: {before - after}")
   ```

### Fase 5: Trova anomalie (5 minuti)

8. **Controlla valori anomali nei gol (nessuna partita ha >10 gol):**

   Filtra le righe dove `HomeGoals > 10` o `AwayGoals > 10` o dove sono negativi.

   ```python
   anomalie = step4.filter(
       # Scrivi le condizioni per trovare gol anomali
       # (> 10 o < 0 per HomeGoals e AwayGoals)
   )
   print(f"Righe con gol anomali: {anomalie.count()}")
   anomalie.show()
   ```

   **Suggerimento**: usa `|` per combinare le condizioni e metti le parentesi attorno a ogni condizione.

9. **Controlla che il possesso sia tra 0 e 1:**

   ```python
   poss_anomalie = step4.filter(
       # Scrivi la condizione: possessiontime_home > 1 o < 0
   )
   print(f"Righe con possesso anomalo: {poss_anomalie.count()}")
   ```

### Fase 6: Log finale (5 minuti)

10. **Stampa il riepilogo della pulizia:**

    ```python
    print("\n=== RIEPILOGO PULIZIA ===")
    print(f"Righe originali:    {raw_df.count()}")
    print(f"Dopo selezione:     {df.count()}")
    print(f"Dopo dropna:        {step1.count()}")
    print(f"Dopo fillna num:    {step2.count()}")
    print(f"Dopo fillna testo:  {step3.count()}")
    print(f"Dopo dedup:         {step4.count()}")
    print(f"Anomalie gol:       {anomalie.count()}")
    ```

## Output atteso

- Schema stampato con 16 colonne
- Conteggio null per ogni colonna
- Log con conteggi progressivi
- 0 anomalie nei gol (dataset pulito)

## Checkpoint

- [ ] CSV caricato con 3.788 righe
- [ ] 16 colonne selezionate
- [ ] Null contati per ogni colonna
- [ ] `dropna` su colonne critiche eseguito
- [ ] `fillna` su colonne numeriche e testo
- [ ] Duplicati rimossi
- [ ] Anomalie verificate
- [ ] Log riepilogativo stampato

## Troubleshooting

| Problema                   | Soluzione                                        |
| -------------------------- | ------------------------------------------------ |
| "Path does not exist"      | Controlla che il CSV sia nella cartella corretta |
| `inferSchema` molto lento  | Normale per 3.788 righe - aspetta                |
| Colonne con spazi nei nomi | Usa backtick: ``col("`Nome Colonna`")``          |

## Cleanup

```python
spark.stop()
```
