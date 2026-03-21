# Lab 04 - Da Hadoop a Spark: perché usiamo Python

## Obiettivo

Costruire una mini-timeline dei Big Data, confrontare Hadoop e Spark, e completare un quiz di autovalutazione.

## Durata (timebox)

30 minuti

## Prerequisiti

- Lab 01-03 completati (concetti Big Data, cluster, partizionamento)

## Scenario

Il tuo responsabile ti chiede: "Perché usiamo Spark e Python invece di Hadoop? Preparami un riassunto semplice così lo capiscono tutti."

## Step

1. **Crea una mini-timeline**: metti in ordine cronologico questi eventi e aggiungi una frase di descrizione per ciascuno.

   Eventi da ordinare:
   - Apache Spark diventa progetto Apache (2010)
   - Google pubblica il paper su MapReduce (2004)
   - Spark supera Hadoop in popolarità (2014)
   - PySpark (interfaccia Python per Spark) diventa stabile (2015)
   - Nasce Hadoop (2006)
   - Google pubblica il paper su GFS (2003)

   | Anno | Evento | Descrizione (1 frase) |
   | ---- | ------ | --------------------- |
   | 2003 | ?      | ?                     |
   | 2004 | ?      | ?                     |
   | 2006 | ?      | ?                     |
   | 2010 | ?      | ?                     |
   | 2014 | ?      | ?                     |
   | 2015 | ?      | ?                     |

2. **Tabella Hadoop vs Spark**: compila questa tabella con quello che hai imparato.

   | Caratteristica                    | Hadoop MapReduce        | Apache Spark |
   | --------------------------------- | ----------------------- | ------------ |
   | Dove salva i risultati intermedi? | Su disco (lento)        | ?            |
   | Linguaggio principale             | Java                    | ?            |
   | Velocità                          | Lento                   | ?            |
   | Facilità d'uso                    | Difficile               | ?            |
   | Quando usarlo                     | Sistemi vecchi (legacy) | ?            |

3. **Quiz di autovalutazione**: rispondi a queste domande in 1–2 frasi.

   a) Perché Hadoop scriveva i risultati su disco dopo ogni passaggio?

   b) Perché Spark è più veloce? (suggerimento: dove tiene i dati?)

   c) Cos'è PySpark in una frase?

   d) Quando è meglio usare pandas invece di PySpark?

   e) Dobbiamo installare Java per usare PySpark? (suggerimento: `pip install pyspark`...)

4. **Bonus - paragone vita reale**: inventa un'analogia per spiegare la differenza tra Hadoop e Spark a qualcuno che non sa nulla di informatica.

   Esempio: _"Hadoop è come cucinare mettendo ogni ingrediente in frigo tra un passaggio e l'altro. Spark è come tenere tutto sul bancone - molto più veloce."_

## Output atteso

- Timeline con 6 eventi in ordine cronologico
- Tabella comparativa Hadoop vs Spark compilata
- Quiz con 5 risposte corrette
- (Bonus) Un'analogia originale

## Checkpoint

- [ ] La timeline ha tutti e 6 gli eventi nell'ordine corretto
- [ ] La tabella ha tutte le caselle compilate
- [ ] Le risposte al quiz sono ragionevoli e corrette
- [ ] (Bonus) L'analogia è comprensibile anche a un non-tecnico

## Troubleshooting rapido

| Problema                  | Soluzione                                                                                              |
| ------------------------- | ------------------------------------------------------------------------------------------------------ |
| Non ricordo le date       | Rivedi le slide della lezione o cerca "hadoop spark timeline"                                          |
| Non so rispondere al quiz | Le risposte sono tutte nelle slide - rileggi con calma                                                 |
| Non mi viene un'analogia  | Pensa a un'attività quotidiana con passaggi intermedi (cucinare, costruire con i Lego, fare i compiti) |

## Cleanup obbligatorio

- Nessun cleanup richiesto (lab teorico)

## Parole chiave Google (screenshot/guide)

- "hadoop vs spark comparison simple"
- "apache spark history timeline"
- "why pyspark instead of hadoop"
