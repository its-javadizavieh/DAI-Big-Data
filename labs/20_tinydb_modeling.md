# Lab 20 - TinyDB: modellazione documenti e query semplici

## Obiettivo

Caricare una versione semplice del dataset Serie A in TinyDB, confrontare documenti flat e nested, e capire quale struttura è più facile da usare per studenti principianti.

## Durata

30 minuti

## Prerequisiti

- Lab 16 completato
- Dataset `serie_a_coppa_italia_2015_2023.csv`

## Step

### Fase 1: Crea documenti flat (10 minuti)

```python
import csv
from tinydb import TinyDB

with open("serie_a_coppa_italia_2015_2023.csv", "r", encoding="utf-8") as f:
    reader = csv.DictReader(f)
    rows = []
    for row in reader:
        rows.append({
            "match_id": int(row["ID"]),
            "competition": row["Competition_Name"],
            "season": int(row["Season_End_Year"]),
            "home": row["Home"],
            "away": row["Away"],
            "home_goals": int(row["HomeGoals"]) if row["HomeGoals"] else 0,
            "away_goals": int(row["AwayGoals"]) if row["AwayGoals"] else 0,
            "city": row["venue_city"]
        })
        if len(rows) == 200:
            break

db = TinyDB("serie_a_tinydb.json")
flat_table = db.table("matches_flat")
flat_table.truncate()
flat_table.insert_multiple(rows)
```

### Fase 2: Query semplici (8 minuti)

```python
from tinydb import Query

Match = Query()
print(flat_table.search(Match.home == "Juventus")[:3])
print(flat_table.search(Match.city == "Torino")[:3])
print(flat_table.search(Match.home_goals > 2)[:3])
```

### Fase 3: Crea una versione nested (7 minuti)

```python
nested_table = db.table("matches_nested")
nested_table.truncate()

nested_docs = []
for r in rows[:20]:
    nested_docs.append({
        "match_id": r["match_id"],
        "competition": r["competition"],
        "season": r["season"],
        "teams": {"home": r["home"], "away": r["away"]},
        "score": {"home": r["home_goals"], "away": r["away_goals"]},
        "venue": {"city": r["city"]}
    })

nested_table.insert_multiple(nested_docs)
print(nested_table.all()[:2])
```

### Fase 4: Riflessione (5 minuti)

Scrivi 4-5 righe:

- quale struttura è più facile da interrogare: flat o nested?
- quale useresti per questo corso?
- perché?

## Output atteso

- 200 documenti flat inseriti
- Query funzionanti su TinyDB
- 20 documenti nested creati
- Confronto scritto tra i due modelli

## Checkpoint

- [ ] File TinyDB creato
- [ ] Tabella `matches_flat` popolata
- [ ] Query base eseguite
- [ ] Tabella `matches_nested` popolata
- [ ] Confronto finale scritto
