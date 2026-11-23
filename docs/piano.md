# Piano di lavoro

**Regola:** prima si rifà il risultato degli autori, poi si cambia una cosa alla volta.
**Validazione, sempre la stessa:** addestro su 20 persone, provo sulla 21ª, a rotazione (leave-one-subject-out).
**Scrittura:** una o due pagine di relazione alla fine di ogni passo.

---

## Fatto
- Esplorazione e controllo del dataset
- Figura 4 riprodotta su 21 soggetti, differenze dal paper spiegate
- Feature usate dagli autori → `docs/feature_autori.md`
- Passi 1–2 (notebook 02, guida PDF dei passi 1-2)
- Analisi delle due persone su cui il modello sbaglia (notebook 02, Parte 4)
- Aggiornamento e domande per la prof → `docs/guide/03_preparazione_call.pdf`
- Call del 6 novembre: binario chiuso, si passa al ternario
- Primo giro del ternario, ablation e MLP (notebook 03)

---

## Passo 1 — Rifare la loro tabella ✅
**Obiettivo:** gli stessi dati usati dagli autori.
- una riga ogni 30 secondi, per tutte le persone
- colonne: `bpm`, `stress_skin`, `temp_sk1`, media / dev. standard / varianza di accX, accY, accZ
- etichetta: riposo = 0, calcolo + discorso + corsa = 1
- normalizzazione per persona

**Dati mancanti** *(nuovo)*
- contare quanti valori mancano per colonna e per fase → **Figura 3**
- decidere come trattarli e annotarlo in `decisions.md`
- **controllo:** il modello non deve poter capire la fase dal fatto che un dato manca. Mai usare "dato mancante sì/no" come colonna.

**Fatto quando:** tabella pronta, Figura 3 fatta, decisione sui mancanti scritta.

## Passo 2 — Il loro classificatore binario ✅
**Obiettivo:** avvicinarsi all'89% (Tabella 4).
- Random Forest, kNN, LDA, Decision Tree, Logistic Regression, XGBoost
- accuracy e F1 per ogni classificatore

**Fatto quando:** una tabella con i risultati di tutti e sei.

## Passo 3 — Classificatore ternario (decisione della prof, 6 novembre)
**Obiettivo:** riprodurre il ternario degli autori (Tabella 2 del paper arXiv: RF 0,77).
- classi: riposo = 0, calcolo + discorso = 1, corsa = 2
- stessa tabella del passo 1, stessa validazione
- matrice di confusione 3×3 e accuracy per persona (controllare `ddca342b`)
- **ablation per gruppi**: livello A (tutte / solo fisiologia / solo movimento), livello B (togli un gruppo alla volta)
- notebook `03_ternario.ipynb`

**Fatto (7 novembre, corretto il 16):** 21 persone, 1.170 righe; RF 0,93 ± 0,07, XGB 0,93, MLP 0,93 (senza early stopping, corretta il 22); solo fisiologia 0,74 (cognitivo 0,67, fisico 0,64). Correzioni: `a5e823ad` ultimi 10 minuti del riposo, `ddca342b` assi Y e Z invertiti, pulsanti premuti più volte (`3f27501c`). Permutation importance (Fasi 20–21): contano postura, battito e agitazione. Parte 6: con pause, recupero e preparazione RF scende a 0,81 (paper 0,77), quindi la differenza si spiega probabilmente con le fasi incluse: restiamo sulle 4 fasi principali.

## Passo 4 — Feature dai dati grezzi (in corso, notebook 04)
**Obiettivo:** il contributo nuovo del tirocinio, valutato sul ternario, sempre con e senza movimento.

Un segnale alla volta, dal più facile:
1. ✅ **movimento**: magnitudine √(x²+y²+z²), indipendente dall'orientamento (media, std)
2. ✅ **temperatura della pelle** (non `temp_sk1`, che è la temperatura interna stimata): media, std, pendenza; flusso di calore
3. ✅ **sudore** da `bioz`: livello, pendenza, numero di reazioni, ampiezza
4. ✅ **battito** dal PPG: misurato quanto è utilizzabile; **la variabilità del battito non entra nel modello** (battito visibile solo a riposo, decisione 40)

Stessi blocchi da 30 s del notebook 03.

**Risultati (18 novembre):** senza movimento 0,75 → 0,80 con la temperatura della pelle → 0,83 con il sudore grezzo; cognitivo contro fisico 0,72 → 0,76 → 0,78.

**Fatto (19 novembre):** tre gruppi nel modello (magnitudine, temperatura, sudore), il quarto (PPG) documentato come limite.

## Passo 5 — MLP sul ternario ✅
- prima sulle 12 feature degli autori (0,93, come Random Forest, senza early stopping), poi con le feature nuove
- **Fatto (23 novembre):** con le feature nuove, senza movimento, MLP 0,78 contro Random Forest 0,83; con il movimento 0,93 tutte e due. Le feature nuove aiutano soprattutto Random Forest
- stessa validazione, confronto con i sei classificatori

## Passo 6 — Rete neurale profonda (opzionale)
- sui segnali grezzi, come ultimo capitolo, solo se c'è tempo

## Passo 7 — Conclusioni
- tabella finale; discussione: quale modello, quali feature, stress o movimento?
- binario come replica riuscita, ternario come lavoro principale
- fine lavoro sperimentale 19 dicembre; relazione dopo

## Prossima call con la prof
Nessuna data fissa: la chiediamo quando ternario, feature nuove e MLP sono pronti (obiettivo fine novembre).

## Da confermare con la prof
- Il passo 3 (tutti / solo fisiologia / solo movimento) va fatto? Sono le altre righe della Tabella 4 del paper, quindi rientrerebbe nella replica
- Ha senso provare anche un compito **senza corsa** (riposo contro calcolo + discorso)? Nella corsa il movimento rende lo stress ovvio; togliendola si vedrebbe se il modello riconosce lo stress o solo il movimento
- Normalizzazione per persona: dichiararla come limite o provare una variante con il solo riposo (calibrazione)?
- `ddca342b` e `a5e823ad`: tenerle o escluderle dalla valutazione?
- Finestre da 60 s: rifare anche la tabella degli autori a 60 s?
- Tabella 2 o Tabella 4? (la 2 ha il titolo del binario ma probabilmente i numeri del ternario)

## Da provare, non urgente
- standardizzazione del sudore con la mediana invece della media (passo 4)
