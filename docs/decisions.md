# Decisioni

Ogni scelta importante: cosa ho deciso, perché, alternative scartate.

## 1. Battito pari a zero
**Decisione:** i valori `bpm = 0` sono trattati come dati mancanti.
**Perché:** un battito zero è impossibile, è un errore del sensore. Lasciarlo sporca media e grafici.

## 2. Violin plot con `cut=0`
**Decisione:** i violini si fermano ai valori osservati.
**Perché:** senza, le code si allungano oltre i dati (fino a battiti negativi).

## 3. `stress_skin` è un indice, non una conduttanza
**Osservazione:** `stress_skin` (file emography) è un indice calcolato dal braccialetto, un valore al minuto.
Gli zeri sono valori validi ("nessuna reazione"), non errori.

## 4. `bioz` è la conduttanza cutanea grezza
**Osservazione:** secondo la documentazione Corsano, `value` è una conduttanza in unità da 100 pS, a 25 Hz.
Da usare per l'EDA nel preprocessing.

## Da decidere
- Accelerometro: valori grezzi a 32 Hz o media per finestra, come nel paper?
- Standardizzazione: per persona o su tutti insieme?
- Fase di preparazione del discorso: conta come stress?
