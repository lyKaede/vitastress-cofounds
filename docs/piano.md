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

**Fatto (7 novembre):** 21 persone, 1.165 righe; RF 0,94 ± 0,06, XGB 0,93, MLP 0,88; solo fisiologia 0,74 (cognitivo 0,65, fisico 0,62). Correzioni: `a5e823ad` ultimi 10 minuti del riposo, `ddca342b` assi Y e Z invertiti. La differenza col paper si spiega probabilmente con le fasi incluse (con pause e preparazione RF 0,80): restiamo sulle 4 fasi principali.

## Passo 4 — Feature dai dati grezzi (Matteo)
**Obiettivo:** il contributo nuovo del tirocinio, valutato sul ternario.

Un segnale alla volta, dal più facile:
1. **movimento**: magnitudine √(x²+y²+z²), indipendente dall'orientamento
2. **temperatura**: media, dev. standard, pendenza
3. **sudore** da `bioz`: numero di reazioni, ampiezza, livello di fondo
4. **battito** dal PPG: variabilità del battito (SDNN, RMSSD, pNN50); misurare anche quanta parte del segnale è utilizzabile

Finestre da 60 s che avanzano di 30 s (proposta, da confermare).

**Fatto quando:** nuova tabella con queste feature, aggiunta all'ablation come nuovo gruppo.

## Passo 5 — MLP sul ternario
- prima sulle 12 feature degli autori (primo giro: 0,86), poi con le feature nuove
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
