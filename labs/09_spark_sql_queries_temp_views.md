# Lab 09 — Spark SQL: query e viste temporanee

## Obiettivo

Eseguire query SQL su DataFrame PySpark usando viste temporanee, subquery e CTE.

## Durata (timebox)

30 minuti

## Prerequisiti

- Lab 08 completato (join e aggregazioni)
- Conoscenza base di SQL (SELECT, WHERE, GROUP BY, JOIN)

## Scenario

Devi preparare un report mensile scrivendo query SQL sui dati di vendita dell'azienda.

## Step

1. **Carica e registra**: carica il dataset CSV e registralo come vista.
   ```python
   df.createOrReplaceTempView("sales")
   ```
2. **Query base**: scrivi una SELECT con WHERE, GROUP BY e ORDER BY.
3. **HAVING**: filtra i gruppi con HAVING (es. vendite totali > 10000).
4. **Subquery**: trova i prodotti con vendite superiori alla media.
   ```sql
   SELECT product, sales FROM sales WHERE sales > (SELECT AVG(sales) FROM sales)
   ```
5. **CTE**: riscrivi la subquery usando WITH.
6. **JOIN in SQL**: crea un secondo DataFrame, registralo, e fai un JOIN in SQL.
7. **Window function**: classifica i prodotti per vendite all'interno di ogni categoria.
   ```sql
   SELECT product, category, sales,
          RANK() OVER (PARTITION BY category ORDER BY sales DESC) AS rank
   FROM sales
   ```
8. **Confronto**: riscrivi la query #2 usando la DataFrame API e confronta i risultati.

## Output atteso

- Almeno 5 query SQL diverse eseguite con risultati stampati
- Il confronto DataFrame API vs SQL produce lo stesso risultato

## Checkpoint

- [ ] createOrReplaceTempView eseguito senza errori
- [ ] Almeno 1 subquery e 1 CTE funzionanti
- [ ] Window function RANK() restituisce risultati sensati
- [ ] Confronto API/SQL mostra risultati identici

## Troubleshooting rapido

- "Table or view not found": hai dimenticato createOrReplaceTempView o hai sbagliato il nome
- "Syntax error in SQL": controlla le virgolette triple e i punti e virgola (non servono in spark.sql)

## Cleanup obbligatorio

```python
spark.stop()
```

## Parole chiave Google (screenshot/guide)

- "spark sql createOrReplaceTempView example"
- "spark sql window functions tutorial"
- "spark sql CTE common table expression"
