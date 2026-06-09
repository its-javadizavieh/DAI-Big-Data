# Soluzioni Lab 04 — Da Hadoop a Spark: perché usiamo Python

## Esercizio 1: Mini-timeline Big Data

| Anno | Evento                             | Descrizione                                                                 |
| ---- | ---------------------------------- | --------------------------------------------------------------------------- |
| 2003 | Google pubblica paper su GFS       | Google File System — sistema di file distribuito per gestire enormi dataset |
| 2004 | Google pubblica paper su MapReduce | Modello di elaborazione distribuita: dividere (Map) e combinare (Reduce)    |
| 2006 | Nasce Hadoop                       | Yahoo! crea l'implementazione open-source di GFS + MapReduce                |
| 2010 | Spark diventa progetto Apache      | Nato a UC Berkeley, introduce elaborazione in-memory (molto più veloce)     |
| 2014 | Spark supera Hadoop in popolarità  | Spark 10-100× più veloce; adozione enterprise accelera                      |
| 2015 | PySpark diventa stabile            | API Python per Spark matura → accessibile senza conoscere Java/Scala        |

## Esercizio 2: Tabella comparativa

| Caratteristica        | Hadoop MapReduce              | Apache Spark                                 |
| --------------------- | ----------------------------- | -------------------------------------------- |
| Risultati intermedi   | Su disco (lento)              | **In memoria RAM** (veloce)                  |
| Linguaggio principale | Java                          | **Python (PySpark), Scala, Java, R**         |
| Velocità              | Lento (I/O disco a ogni step) | **10-100× più veloce**                       |
| Facilità d'uso        | Difficile (Java boilerplate)  | **Semplice** (API DataFrame di alto livello) |
| Quando usarlo         | Sistemi vecchi (legacy)       | **Nuovo sviluppo, analisi interattiva, ML**  |

## Esercizio 3: Quiz di autovalutazione

**a)** Hadoop scriveva su disco per _fault tolerance_: se un nodo moriva, i risultati intermedi erano salvati e non andavano persi. Purtroppo questo rendeva tutto molto lento.

**b)** Spark è più veloce perché tiene i dati **in RAM** (memoria) invece che su disco. La RAM è ~100× più veloce del disco.

**c)** PySpark è l'interfaccia Python per Apache Spark — permette di usare la potenza di Spark scrivendo codice Python.

**d)** pandas è meglio quando i dati stanno in memoria di un singolo computer (< ~8 GB). PySpark serve quando i dati sono troppo grandi.

**e)** No! `pip install pyspark` installa tutto il necessario automaticamente (inclusa una versione di Java integrata).

## Bonus: Analogia

**Hadoop** è come studiare per un esame mettendo ogni foglio di appunti in uno scaffale diverso dopo averlo letto. Ogni volta che ti serve, devi alzarti e andare a recuperarlo.

**Spark** è come tenere tutti gli appunti sulla scrivania — li hai subito sotto mano, senza alzarti. Molto più veloce!
