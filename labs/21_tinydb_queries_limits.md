# Lab 21 - TinyDB: query utili, tabelle di riepilogo, e limiti pratici

## Obiettivo

Usare TinyDB in modo più intelligente: cercare con condizioni combinate, aggiornare etichette, e creare una piccola tabella di riepilogo invece di rileggere sempre i dati grezzi.

## Durata

30 minuti

## Prerequisiti

- Lab 17 completato
- Lab 20 completato

## Step

### Fase 1: Apri il database Superstore (5 minuti)

```python
from tinydb import TinyDB, Query

db = TinyDB("superstore_tinydb.json")
orders = db.table("orders")
Order = Query()

print(f"Documenti disponibili: {len(orders)}")
```

### Fase 2: Query combinate (10 minuti)

```python
west_tech = orders.search((Order.region == "West") & (Order.category == "Technology"))
high_sales = orders.search(Order.sales > 500)
west_high = orders.search((Order.region == "West") & (Order.sales > 500))

print(len(west_tech), len(high_sales), len(west_high))
print(west_high[:3])
```

### Fase 3: Aggiungi una label (5 minuti)

```python
orders.update({"high_value": True}, Order.sales > 500)
print(orders.search(Order.high_value == True)[:3])
```

### Fase 4: Crea una tabella summary (10 minuti)

```python
summary = db.table("sales_by_region")
summary.truncate()

all_orders = orders.all()
regions = sorted({doc["region"] for doc in all_orders})
summary_docs = []

for region in regions:
    rows = [doc for doc in all_orders if doc["region"] == region]
    total_sales = round(sum(doc["sales"] for doc in rows), 2)
    summary_docs.append({
        "region": region,
        "num_orders": len(rows),
        "total_sales": total_sales
    })

summary.insert_multiple(summary_docs)
print(summary.all())
```

Poi scrivi 3 righe:

- perché una tabella `sales_by_region` è più comoda di rileggere tutti gli ordini ogni volta?
- perché TinyDB va bene per questo riepilogo ma non per dataset enormi?

## Output atteso

- Query combinate funzionanti
- Campo `high_value` aggiunto
- Tabella `sales_by_region` creata
- Riflessione scritta sui limiti di TinyDB

## Checkpoint

- [ ] Query combinate eseguite
- [ ] Label aggiunta con update
- [ ] Summary table creata
- [ ] Limiti di TinyDB spiegati
