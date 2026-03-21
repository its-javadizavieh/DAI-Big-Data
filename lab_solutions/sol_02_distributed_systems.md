# Soluzioni Lab 02 - Sistemi distribuiti: cluster, nodi, parallelismo

## Esercizio 1: Diagramma del cluster

```
              +--------+
              |  BOSS  |
              +--------+
             /  |    |  \
            /   |    |   \
   +------+ +------+ +------+ +------+
   |Help 1| |Help 2| |Help 3| |Help 4|
   |3.75GB| |3.75GB| |3.75GB| |3.75GB|
   +------+ +------+ +------+ +------+
```

- 4 partizioni da ~3.75 GB ciascuna (15 GB ÷ 4)
- Ogni helper ha 8 GB di RAM → 3.75 GB ci sta comodamente

## Esercizio 2: Divisione del dataset

- **Partizioni**: 4 (una per helper)
- **Dimensione per partizione**: 15 GB ÷ 4 = 3.75 GB
- **Assegnazione**: Partizione 1 → Helper 1, Partizione 2 → Helper 2, ecc.
- Ogni partizione sta nella RAM del helper (3.75 GB < 8 GB) ✓

## Esercizio 3: Simulazione compito

**Compito**: "Conta quanti studenti hanno preso più di 8 in matematica"

1. **Boss** invia il compito a tutti gli helper: _"Conta studenti con voto_mate > 8 nella tua partizione"_
2. I 4 **helper** lavorano in parallelo (contemporaneamente!):
   - Helper 1: "Ho trovato 45.230 studenti"
   - Helper 2: "Ho trovato 51.102 studenti"
   - Helper 3: "Ho trovato 48.750 studenti"
   - Helper 4: "Ho trovato 47.918 studenti"
3. **Boss** riceve i risultati parziali e li combina:
   - Totale: 45.230 + 51.102 + 48.750 + 47.918 = **193.000 studenti**

**Tempo**: Se un singolo computer impiega 4 minuti, il cluster impiega circa 1 minuto (4× più veloce).

## Esercizio 4: Simulazione guasto

L'Helper 3 si spegne a metà del lavoro.

1. **Il boss se ne accorge** perché l'Helper 3 smette di inviare _heartbeat_ (segnale "sono vivo" ogni pochi secondi)
2. **Il boss controlla le copie**: se esiste una replica dei dati dell'Helper 3 su un altro nodo, la usa
3. **Il boss ri-assegna il lavoro**: la partizione dell'Helper 3 viene assegnata a un altro helper che ha capacità
4. **Il lavoro NON ricomincia da zero**: gli altri 3 helper hanno già finito la loro parte. Solo la parte dell'Helper 3 viene ricalcolata

## Esercizio 5: Scale-up vs Scale-out

|                            | Scale-up (più potente)                | Scale-out (più computer)                 |
| -------------------------- | ------------------------------------- | ---------------------------------------- |
| **Costo**                  | Molto caro (server enterprise)        | Economico (tanti PC normali)             |
| **Limite massimo**         | C'è un limite fisico (max RAM/CPU)    | Quasi illimitato (aggiungi nodi)         |
| **Se si rompe**            | Tutto fermo (single point of failure) | Solo un nodo fermo, gli altri continuano |
| **Per il nostro scenario** | ❌ Non praticabile (15 GB > 8 GB RAM) | ✅ Soluzione ideale (4 helper da 8 GB)   |
