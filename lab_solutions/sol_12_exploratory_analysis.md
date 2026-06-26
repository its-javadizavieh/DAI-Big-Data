# Soluzioni Lab 12 — Analisi esplorativa: dataset del corso (Serie A e Coppa Italia)

## Setup

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, count, sum, avg, max, min, when, round as spark_round, desc

spark = SparkSession.builder.appName("Sol12_EDA").getOrCreate()

df = spark.read.csv("serie_a_coppa_italia_2015_2023.csv", header=True, inferSchema=True)
print(f"Righe: {df.count()}")        # ~3788
print(f"Colonne: {len(df.columns)}") # ~228
```

## Step 2 — Colonne principali

```python
cols = ["Competition_Name", "Season_End_Year", "Date",
        "Home", "Away", "HomeGoals", "AwayGoals",
        "possessiontime_home", "shots_total_home",
        "yellow_cards_home", "red_cards_home"]

df.select(cols).show(5)
```

## Step 3 — Match per competizione

```python
df.groupBy("Competition_Name").count().show()
```

**Output atteso:**

```
+----------------+-----+
|Competition_Name|count|
+----------------+-----+
|         Serie A| 3420|
|    Coppa Italia|  368|
+----------------+-----+
```

_(I numeri possono variare leggermente)_

## Step 4 — Media gol per stagione

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

**Output atteso:** media tra 2.5 e 3.2 gol per partita, variabile per stagione.

## Step 5 — Top 10 vittorie in casa

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

**Output atteso:** Juventus, Napoli, Inter, Milan nelle prime posizioni.

## Step 6 — Squadre più indisciplinate

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

## Step 7 — Arbitri con più rossi

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

## Step 8 — Fattore campo

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

## Step 9 — Risposte alle domande

**In quale stagione il fattore campo è stato più forte?**
Generalmente le stagioni 2015-2017 mostrano percentuali più alte di vittorie in casa (>45%). Dopo il COVID (2020-2021, con stadi vuoti), il fattore campo è calato.

**La percentuale di vittorie in casa è aumentata o diminuita nel tempo?**
Tendenzialmente è diminuita nel tempo, in parte per il livellamento competitivo e in parte per l'effetto delle partite a porte chiuse durante il COVID.

## Step 10 — Top 5 partite con più gol

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

## Cleanup

```python
spark.stop()
```