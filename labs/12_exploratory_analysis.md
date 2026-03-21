# Lab 12 — Analisi esplorativa: Serie A e Coppa Italia

## Obiettivo

Condurre un'analisi esplorativa (EDA) completa sul dataset di calcio italiano, rispondendo a domande di business con PySpark.

## Durata

30 minuti

## Prerequisiti

- Lab 06-11 completati
- Dataset `serie_a_coppa_italia_2015_2023.csv` nella cartella di lavoro

## Dataset

**serie_a_coppa_italia_2015_2023.csv** — 3.788 partite (Serie A + Coppa Italia), stagioni 2015-2023.

---

## Step

### Fase 1: Carica e conosci i dati (5 minuti)

1. **Carica il dataset:**

   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.functions import (
       col, count, sum, avg, max, min, when, round as spark_round, desc
   )

   spark = SparkSession.builder.appName("Lab23_EDA").getOrCreate()

   df = spark.read.csv("serie_a_coppa_italia_2015_2023.csv",
                       header=True, inferSchema=True)

   print(f"Righe: {df.count()}")
   print(f"Colonne: {len(df.columns)}")
   ```

2. **Esplora le colonne principali:**

   ```python
   cols = ["Competition_Name", "Season_End_Year", "Date",
           "Home", "Away", "HomeGoals", "AwayGoals",
           "possessiontime_home", "shots_total_home",
           "yellow_cards_home", "red_cards_home"]

   df.select(cols).show(5)
   ```

3. **Quanti match per competizione?**

   ```python
   df.groupBy("Competition_Name").count().show()
   ```

### Fase 2: Gol e vittorie (8 minuti)

4. **Media gol per stagione:**

   ```python
   df.createOrReplaceTempView("matches")

   gol_per_stagione = spark.sql("""
       SELECT
           Season_End_Year AS stagione,
           ROUND(AVG(HomeGoals + AwayGoals), 2) AS media_gol_partita,
           SUM(HomeGoals + AwayGoals) AS gol_totali,
           COUNT(*) AS partite
       FROM matches
       WHERE Competition_Name = 'Serie A'
       GROUP BY Season_End_Year
       ORDER BY Season_End_Year
   """)
   print("Media gol per stagione (Serie A):")
   gol_per_stagione.show()
   ```

5. **Classifica vittorie in casa (tutte le stagioni):**

   ```python
   vittorie_casa = spark.sql("""
       SELECT
           Home AS squadra,
           COUNT(*) AS vittorie_casa
       FROM matches
       WHERE Competition_Name = 'Serie A'
         AND HomeGoals > AwayGoals
       GROUP BY Home
       ORDER BY vittorie_casa DESC
       LIMIT 10
   """)
   print("Top 10 vittorie in casa (Serie A totale):")
   vittorie_casa.show()
   ```

### Fase 3: Analisi disciplinare (7 minuti)

6. **Squadre più indisciplinate (cartellini gialli + rossi):**

   ```python
   disciplina = spark.sql("""
       SELECT
           Home AS squadra,
           SUM(yellow_cards_home) AS gialli,
           SUM(red_cards_home) AS rossi,
           SUM(yellow_cards_home + red_cards_home) AS totale_cartellini,
           COUNT(*) AS partite_casa
       FROM matches
       WHERE Competition_Name = 'Serie A'
       GROUP BY Home
       ORDER BY totale_cartellini DESC
       LIMIT 10
   """)
   print("Top 10 squadre per cartellini (in casa):")
   disciplina.show()
   ```

7. **Arbitri che danno più cartellini rossi:**

   ```python
   arbitri = spark.sql("""
       SELECT
           Referee AS arbitro,
           SUM(red_cards_home + red_cards_away) AS rossi_totali,
           COUNT(*) AS partite_arbitrate
       FROM matches
       WHERE Competition_Name = 'Serie A'
         AND Referee IS NOT NULL
       GROUP BY Referee
       HAVING COUNT(*) > 20
       ORDER BY rossi_totali DESC
       LIMIT 10
   """)
   print("Arbitri con più espulsioni (min 20 partite):")
   arbitri.show()
   ```

### Fase 4: Fattore campo e statistiche (10 minuti)

8. **Fattore campo: vittorie casa vs trasferta vs pareggi:**

   ```python
   fattore = spark.sql("""
       SELECT
           Season_End_Year AS stagione,
           COUNT(*) AS partite,
           SUM(CASE WHEN HomeGoals > AwayGoals THEN 1 ELSE 0 END) AS vittorie_casa,
           SUM(CASE WHEN HomeGoals < AwayGoals THEN 1 ELSE 0 END) AS vittorie_trasferta,
           SUM(CASE WHEN HomeGoals = AwayGoals THEN 1 ELSE 0 END) AS pareggi,
           ROUND(100.0 * SUM(CASE WHEN HomeGoals > AwayGoals THEN 1 ELSE 0 END)
                 / COUNT(*), 1) AS pct_vittorie_casa
       FROM matches
       WHERE Competition_Name = 'Serie A'
       GROUP BY Season_End_Year
       ORDER BY Season_End_Year
   """)
   print("Fattore campo per stagione:")
   fattore.show()
   ```

9. **Rispondi a queste domande (scrivi le risposte):**
   - In quale stagione il fattore campo è stato più forte?
   - La percentuale di vittorie in casa è aumentata o diminuita nel tempo?

10. **Top 5 partite con più gol:**

    ```python
    spark.sql("""
        SELECT
            Date, Home, Away, HomeGoals, AwayGoals,
            (HomeGoals + AwayGoals) AS gol_totali
        FROM matches
        WHERE Competition_Name = 'Serie A'
        ORDER BY gol_totali DESC
        LIMIT 5
    """).show()
    ```

## Output atteso

- 3.788 righe con ~3.420 Serie A + ~368 Coppa Italia
- Media gol per stagione (Serie A)
- Top 10 vittorie in casa
- Top 10 squadre per cartellini
- Arbitri più severi
- Fattore campo per stagione con percentuali
- Top 5 partite con più gol

## Checkpoint

- [ ] Dataset caricato con conteggio per competizione
- [ ] Media gol per stagione calcolata
- [ ] Classifica vittorie in casa
- [ ] Analisi cartellini completata
- [ ] Fattore campo calcolato con percentuali
- [ ] Domande di analisi risposte
- [ ] Top 5 partite con più gol trovate

## Troubleshooting

| Problema                      | Soluzione                                               |
| ----------------------------- | ------------------------------------------------------- |
| Colonne numeriche come string | `inferSchema=True` dovrebbe risolvere                   |
| NULL nei cartellini           | Usa `COALESCE(col, 0)` o `fillna(0)` prima dell'analisi |
| Pochi risultati per arbitro   | `HAVING COUNT(*) > 20` filtra arbitri con poche partite |

## Cleanup

```python
spark.stop()
```
