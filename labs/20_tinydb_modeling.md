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

Carica le prime 200 righe del CSV e crea documenti flat (senza nesting) con questi campi:
`match_id`, `competition`, `season`, `home`, `away`, `home_goals`, `away_goals`, `city`

```python
import csv
from tinydb import TinyDB

with open("serie_a_coppa_italia_2015_2023.csv", "r", encoding="utf-8") as f:
    reader = csv.DictReader(f)
    rows = []
    for row in reader:
        # Crea un dizionario flat con i campi indicati sopra
        # Converti match_id, season, home_goals, away_goals a int
        # Usa row["ID"], row["Competition_Name"], row["Season_End_Year"],
        # row["Home"], row["Away"], row["HomeGoals"], row["AwayGoals"],
        # row["venue_city"]
        rows.append({
            # Scrivi qui il tuo codice
        })
        if len(rows) == 200:
            break

db = TinyDB("serie_a_tinydb.json")
flat_table = db.table("matches_flat")
flat_table.truncate()
# Inserisci i documenti
# Scrivi qui il tuo codice
```

**Suggerimento**: usa `int(row["HomeGoals"]) if row["HomeGoals"] else 0` per gestire i campi vuoti.

### Fase 2: Query semplici (8 minuti)

Scrivi le query per:

1. Cercare le partite della Juventus in casa
2. Cercare le partite giocate a Torino
3. Cercare le partite con piu' di 2 gol in casa

```python
from tinydb import Query

Match = Query()

# 1. Partite Juventus in casa (stampa le prime 3)
# 2. Partite a Torino (stampa le prime 3)
# 3. Partite con home_goals > 2 (stampa le prime 3)

# Scrivi qui il tuo codice
```

**Suggerimento**: usa `flat_table.search(Match.campo == "valore")[:3]`.

### Fase 3: Crea una versione nested (7 minuti)

Usando i primi 20 documenti flat, crea una versione nested dove:

- `teams` contiene `{"home": ..., "away": ...}`
- `score` contiene `{"home": ..., "away": ...}`
- `venue` contiene `{"city": ...}`

```python
nested_table = db.table("matches_nested")
nested_table.truncate()

nested_docs = []
for r in rows[:20]:
    nested_docs.append({
        "match_id": r["match_id"],
        "competition": r["competition"],
        "season": r["season"],
        # Crea i sotto-documenti "teams", "score", "venue"
        # Scrivi qui il tuo codice
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
