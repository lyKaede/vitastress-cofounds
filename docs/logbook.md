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

---

## 16 novembre — Correzione degli orari

**Fatto**
- Confrontato il notebook 03 con quello del collega: trovati 5 casi di pulsanti premuti più volte nelle annotazioni
- Corretta `trova_orari` (Fase 1): ultimo inizio che ha una fine dopo di sé, con la prima fine successiva; avviso quando manca l'orario della fine
- Notebook 03 rieseguito, README e grafici aggiornati

**Risultati (ternario, 21 persone, 1.170 righe)**
- RF 0,93 ± 0,07 · XGB 0,93 · LR 0,91 · LDA 0,90 · kNN 0,89 · DT 0,85 · MLP 0,86
- solo fisiologia 0,74: riposo 0,89, cognitivo 0,67, fisico 0,64
- `3f27501c` 1,00

**Capito**
- RF con 5 semi casuali diversi varia fra 0,93 e 0,94: una differenza di un punto non è un risultato

**Pomeriggio: permutation importance e Parte 6**
- Notebook 03, Fasi 20–21: permutation importance per gruppo sulla persona esclusa. Contano postura (0,26), battito (0,22) e agitazione (0,20); sudore e temperatura quasi zero. La MDI sopravvalutava l'agitazione (0,39)
- Notebook 03, Parte 6 (Fasi 24–26): aggiungendo pause, recupero e preparazione del discorso RF scende da 0,93 a 0,81 (paper 0,77). Crolla lo stress cognitivo (0,66): le pause, seduti con il battito ancora alto, somigliano al calcolo
- Guida PDF del notebook 03

**Prossimo passo**
- Feature nuove dai segnali grezzi: magnitudine del movimento, temperatura, sudore (`bioz`), PPG

---

## 17 novembre — Feature nuove: magnitudine e temperatura della pelle

**Fatto**
- Notebook 04 (`04_feature_nuove.ipynb`): stessa tabella del notebook 03 più 6 colonne nuove da due segnali grezzi
- Magnitudine del movimento (media e std): misura del movimento che non dipende dall'orientamento del braccialetto
- Temperatura della pelle e flusso di calore dal file `heat_flux_sensor_temperature`

**Scoperto**
- `temp_sk1`, la temperatura usata dagli autori, è la temperatura interna stimata (`cbt`), quasi costante: per questo non serviva
- La pelle si scalda a riposo e nel calcolo, si raffredda nella corsa e nel discorso (pendenza media per fase: +0,21 · +0,10 · −0,19 · −0,02 °C al minuto)
- La colonna `body_pose` dell'accelerometro vale sempre 1: inutile

**Risultati (Random Forest, 1.170 righe)**
- Senza movimento: fisiologia degli autori 0,74 → fisiologia nuova **0,80** (media su 5 semi; cognitivo 0,67 → 0,72, fisico 0,64 → 0,75)
- Controllo senza riposo, cognitivo contro fisico: 0,70 → **0,76** (classe più numerosa 0,54)
- Con la magnitudine: 0,88–0,89 (con fisiologia degli autori o nuova: con il movimento la temperatura non aggiunge niente)
- Con tutto (autori + nuove): 0,94, come il notebook 03

**Prossimo passo**
- Sudore da `bioz`: reazioni, ampiezza, livello di fondo

---

## 18 novembre — Feature nuove: sudore dal segnale grezzo

**Fatto**
- Aggiornamento per la prof (`aggiornamento_tirocinio_18_novembre.pdf`), nello stile del documento del 6 novembre
- Notebook 04, Parte 4: sudore dal segnale grezzo `bioz` (conduttanza della pelle, 25 Hz): livello, pendenza, reazioni, ampiezza

**Scoperto**
- Il segnale sale quando si suda: dopo la corsa resta alto per molti minuti
- A riposo il segnale sta ancora salendo (l'elettrodo si assesta): il livello dipende dall'ordine delle fasi
- Nel discorso ci sono buchi improvvisi dovuti ai movimenti del polso
- Reazioni per blocco da 30 s: riposo 0,78 · calcolo 1,78 · corsa 1,51 · discorso 2,20
- L'indice `stress_skin` da solo fa 0,36 (quasi il caso), il segnale grezzo 0,61

**Risultati (Random Forest, 1.167 righe, media su 5 semi)**
- Senza movimento: autori 0,75 → + temperatura 0,80 → + sudore grezzo **0,83** (cognitivo 0,69 → 0,76)
- Cognitivo contro fisico, senza riposo: 0,72 → 0,76 → **0,78**
- Con la magnitudine: 0,90; con tutto: 0,95

**Prossimo passo**
- Variabilità del battito dal PPG

---

## 19 novembre — Battito dal PPG: misurato, non usato

**Fatto**
- Notebook 04, Parte 5: quanto si vede il battito nel PPG del polso, fase per fase, e quanto coprono gli intervalli del braccialetto
- Provati tutti i canali (verde, rosso, infrarosso, `ppg3`) e un primo rilevatore di battiti con `scipy`: affidabile solo a riposo

**Scoperto**
- Finestre da 10 s con il battito visibile: riposo 71% · calcolo 15% · discorso 4% · corsa 3%
- Intervalli del braccialetto (`rr_interval`): 85% del riposo · 32% del calcolo · 11% del discorso · 0% della corsa
- Appena si parla, si scrive, si gesticola o si corre, i movimenti del polso coprono il battito

**Deciso**
- La variabilità del battito resta documentata ma fuori dal modello (decisione 40): sarebbe una misura del movimento, non dello stress
- MLP con le feature nuove: in attesa (decisione 41)

---

## 22 novembre — Correzione della MLP

**Fatto**
- Letto l'aggiornamento del collega (19 novembre): MLP, feature dai segnali grezzi, CNN 1D. Anche lui ha trovato che `temp_sk1` = `cbt`, ma ha tenuto `cbt` invece della temperatura della pelle
- La sua MLP fa 0,93, la nostra 0,86: la causa era `early_stopping=True` (decisione 42). Tolto nel notebook 03 (Fase 22) e rieseguito

**Risultati (ternario, MLP senza early stopping)**
- Accuracy 0,93 ± 0,07, come Random Forest (0,93) e XGBoost (0,93); con 5 semi 0,92–0,93
- Recall riposo 0,95 · cognitivo 0,91 · fisico 0,94; una sola persona sotto 0,80 (`a360c459`, 0,74)
- Solo fisiologia 0,75 (Random Forest 0,74)
- Con le feature nuove, senza movimento, prova veloce: MLP 0,80 contro Random Forest 0,83

**Capito**
- La MLP non è peggio dei classificatori classici: li pareggia. Un modello più complesso non aiuta, ma non perché la rete sia instabile

**Prossimo passo**
- Mandare l'aggiornamento alla prof e chiedere la call; parlare con il collega

