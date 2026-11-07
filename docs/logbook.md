# Diario di lavoro

Una voce per sessione: cosa ho fatto, cosa ho scoperto, prossimo passo.

---

## Settimana 1

**Fatto**
- Letto il paper di VitaStress e la versione arXiv con il benchmark
- Corso Google sulle reti neurali, basi di PyTorch
- Scaricato il dataset su Colab: 21 persone, 13 file ciascuna
- Letto il file delle annotazioni e trovato gli orari delle quattro fasi
- Violin plot del battito per una persona, poi delle sei variabili della Figura 4

**Scoperto**
- I nomi degli eventi nelle annotazioni non sono uniformi (`Physical: Start` / `Physical Start`)
- Un battito pari a zero nella fase fisica: errore del sensore
- Le date sono spostate al 2035 per anonimato

---

## Settimana 2 — Figura 4

**Fatto**
- Figura 4 riprodotta su tutti i 21 soggetti, standardizzata per persona
- Confronto con la figura del paper, riquadro per riquadro
- Guida PDF della Figura 4

**Scoperto**
- La figura coincide con quella del paper per tutte e sei le misure
- La coda a −8 della temperatura nel paper è un valore a 0 °C (errore del sensore) non rimosso
- In tre punti il testo del paper descrive andamenti che la sua figura non mostra (sudore, temperatura, accX)
- `stress_skin` è pieno di zeri (20–45%): la standardizzazione appiattisce le differenze fra fasi

---

## Settimana 3 — Tabella e classificatore

**Fatto**
- Indicazioni della prof: replicare il classificatore binario, usare i grezzi per nuove feature, poi MLP
- Documentato quali feature usano gli autori (`feature_autori.md`) e il piano (`piano.md`)
- Passo 1: tabella come gli autori, 1.145 righe da 30 s
- Passo 2: sei classificatori con leave-one-subject-out
- Quattro grafici dei risultati e guida PDF dei passi 1-2

**Risultati**
- Random Forest 0,93 ± 0,11 contro 0,89 ± 0,11 del paper: replica in linea
- 19 persone su 21 sopra 0,85; due intorno a 0,62 (`ddca342b`, `a5e823ad`)
- Le feature di movimento pesano circa il 61% delle decisioni di Random Forest

**Scoperto**
- La pipeline del paper (Figura 3 arXiv) coincide con la nostra
- Dalle loro matrici di confusione: 1.114 righe contro le nostre 1.145
- I titoli delle tabelle del paper sono scambiati: il binario all'89% è la Tabella 4

**Prossimo passo**
- Call di aggiornamento con la prof

---

## Settimana 4 — Preparazione alla call con la prof

**Fatto**
- Riletto lo scambio di mail: il piano concordato è replica → feature dai grezzi → MLP
- Pronta la lista delle feature usate dagli autori, chiesta dalla prof (`feature_autori.md`)
- Preparata la call di aggiornamento sui passi 1 e 2
- Analizzate le due persone su cui il modello sbaglia (notebook 02, Parte 4), con l'aiuto di Claude
- Mandato alla prof il documento di aggiornamento e chiesto un incontro

**Scoperto**
- `ddca342b` ha il braccialetto orientato al contrario: il modello sbaglia 18 riposi su 20 perché usa la postura del polso; con solo la fisiologia arriva a 0,91
- `a5e823ad` sbaglia per la normalizzazione: senza riposo, la media è calcolata solo sullo stress
- Importanza per gruppo: battito 0,35, sudore + temperatura 0,04, medie dell'accelerometro 0,29, std e varianza 0,32

**Da chiedere alla prof**
- Il confronto tutti / solo fisiologia / solo movimento va fatto? Non era nel piano concordato, ma sono righe della Tabella 4 del paper
- Ha senso un compito senza corsa, per capire se il modello riconosce lo stress o solo il movimento?
- Via libera alle feature nuove dai segnali grezzi e ordine con cui calcolarle
- Tabella 2 o Tabella 4 come riferimento
- Normalizzazione per persona: limite o variante con il solo riposo?
- Finestre da 60 secondi

**Prossimo passo**
- Dopo la call: passo 3 se confermato, altrimenti direttamente passo 4

---

## Settimana 5 — Call con la prof e inizio del ternario

**Fatto**
- Call con la prof (6 novembre), preparata con il recap PDF
- Notebook 03: ternario come gli autori, matrice di confusione, ablation per gruppi, MLP (primo giro)
- README della repo arricchito con i grafici del passo 1, del passo 2 e delle due persone difficili

**Decisioni della prof**
- binario chiuso, si continua sul ternario
- to do: ternario, feature nuove dai grezzi, MLP sul ternario
- rete profonda sui grezzi solo se c'è tempo
- `a5e823ad` in sospeso

**Risultati (primo giro, RF, 20 persone)**
- ternario: 0,91 contro 0,77 del paper
- solo fisiologia 0,76 · solo movimento 0,87
- la fisiologia riconosce il riposo (0,91) ma confonde stress cognitivo e fisico (0,68 e 0,66)
- MLP 0,86

**Da capire**
- perché sul ternario siamo 8–14 punti sopra il paper (sul binario erano 0–7)

**Prossimo passo**
- Indagare la differenza con il paper, poi le feature nuove

---

## 7 novembre — Ternario sistemato, lavoro del collega

**Fatto**
- Cercata la causa della differenza col paper sul ternario: normalizzazione e modo di contare non c'entrano; includendo pause, recupero e preparazione del discorso RF scende a 0,80 (paper 0,77). Decisione: restare sulle 4 fasi principali
- Letto il lavoro del collega (stessa pipeline, risultati ancora più alti dei nostri)
- Adottate due sue correzioni: `a5e823ad` con gli ultimi 10 minuti del riposo, `ddca342b` con gli assi Y e Z invertiti (la mia ipotesi X e Z era sbagliata)
- Notebook 03 aggiornato ed eseguito; README con la sezione del ternario
- Animazione della MLP (pagina interattiva)

**Risultati (ternario, 21 persone)**
- RF 0,94 ± 0,06 · XGB 0,93 · MLP 0,88
- solo fisiologia 0,74: riposo 0,91, cognitivo 0,65, fisico 0,62
- `ddca342b` da 0,49 a 1,00 con la correzione degli assi

**Capito**
- per gli autori la corsa è stress fisico: usare il movimento per riconoscerla è corretto; la domanda della tesi è la parte psicologica
- domanda di ricerca: con feature fisiologiche migliori, il braccialetto distingue lo stress psicologico dallo sforzo fisico senza il movimento?

**Prossimo passo**
- Studiare il notebook 03 (Parti 1–5), poi le feature nuove dai segnali grezzi
