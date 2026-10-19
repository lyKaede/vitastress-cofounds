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
- Passo 3: confronto tutti / solo fisiologia / solo movimento
- In attesa della risposta della prof sul compito senza corsa
