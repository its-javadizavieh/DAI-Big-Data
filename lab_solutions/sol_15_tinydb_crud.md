# Soluzioni Lab 15 — TinyDB: setup e CRUD

## Installazione

```bash
pip install tinydb
```

## Create

```python
from tinydb import TinyDB, Query

db = TinyDB("serie_a_db.json")
partite = db.table("partite")
partite.truncate()

partite.insert_multiple([
    {"home": "Juventus", "away": "Roma", "homeGoals": 3, "awayGoals": 1, "city": "Torino"},
    {"home": "Milan", "away": "Inter", "homeGoals": 1, "awayGoals": 2, "city": "Milano"},
    {"home": "Napoli", "away": "Lazio", "homeGoals": 2, "awayGoals": 0, "city": "Napoli"},
    {"home": "Fiorentina", "away": "Atalanta", "homeGoals": 0, "awayGoals": 0, "city": "Firenze"},
    {"home": "Torino", "away": "Bologna", "homeGoals": 4, "awayGoals": 2, "city": "Torino"}
])
```

## Read

```python
Match = Query()

partite.all()
partite.search(Match.homeGoals > 2)
partite.search(Match.city == "Torino")
partite.get(Match.home == "Napoli")
```

## Update

```python
partite.update({"competition": "Serie A"}, Match.home == "Juventus")
partite.update({"draw": True}, Match.homeGoals == Match.awayGoals)
```

## Delete

```python
partite.remove(Match.away == "Atalanta")
print(len(partite))
# Atteso: 4
```