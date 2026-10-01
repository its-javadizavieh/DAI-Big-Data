# Soluzioni Lab 17 — Pulizia dati e trasformazioni batch

## Fase 1: Carica e ispeziona

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, count, when, isnan, trim, to_date

spark = SparkSession.builder.appName("Sol17Cleaning").getOrCreate()

raw_df = spark.read.csv("serie_a_coppa_italia_2015_2023.csv",
                        header=True, inferSchema=True)
print(f"Righe totali: {raw_df.count()}")      # ~3788
print(f"Colonne totali: {len(raw_df.columns)}")  # ~228
```

### Seleziona colonne

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

## Fase 2: Conta i null

```python
null_counts = df.select([
    count(when(col(c).isNull(), c)).alias(c)
    for c in df.columns
])
print("Null per colonna:")
null_counts.show()
```

**Output atteso:** ID, Home, Away, HomeGoals, AwayGoals avranno pochi/zero null. Referee, possessiontime, shots, fouls avranno più null (specialmente per partite più vecchie).

## Fase 3: Gestisci i null

### Colonne critiche

```python
step1 = df.dropna(subset=["ID", "Home", "Away"])
print(f"Dopo dropna critici: {step1.count()} righe")
```

### Colonne numeriche — fillna con 0

```python
numeric_cols = ["shots_total_home", "shots_ongoal_home",
                "yellow_cards_home", "red_cards_home", "fouls_home"]

step2 = step1.fillna(0, subset=numeric_cols)
print(f"Dopo fillna numerici: {step2.count()} righe")
```

### Colonne testo — fillna con "Sconosciuto"

```python
step3 = step2.fillna("Sconosciuto", subset=["Referee"])
print(f"Dopo fillna testo: {step3.count()} righe")
```

## Fase 4: Rimuovi duplicati

```python
before = step3.count()
step4 = step3.dropDuplicates(["ID"])
after = step4.count()
print(f"Duplicati rimossi: {before - after}")
# Atteso: 0 (gli ID sono unici)
```

## Fase 5: Trova anomalie

### Gol anomali

```python
anomalie = step4.filter(
    (col("HomeGoals") > 10) | (col("AwayGoals") > 10) |
    (col("HomeGoals") < 0) | (col("AwayGoals") < 0)
)
print(f"Righe con gol anomali: {anomalie.count()}")
anomalie.show()
# Atteso: 0 righe (il dataset è pulito)
```

### Possesso anomalo

```python
poss_anomalie = step4.filter(
    (col("possessiontime_home") > 1) | (col("possessiontime_home") < 0)
)
print(f"Righe con possesso anomalo: {poss_anomalie.count()}")
# Nota: il possesso potrebbe essere in percentuale (0-100) o in decimale (0-1).
# Controlla i valori reali per capire il formato.
```

## Fase 6: Log finale

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

**Output atteso:**

```
=== RIEPILOGO PULIZIA ===
Righe originali:    3788
Dopo selezione:     3788
Dopo dropna:        3788
Dopo fillna num:    3788
Dopo fillna testo:  3788
Dopo dedup:         3788
Anomalie gol:       0
```

## Cleanup

```python
spark.stop()
```