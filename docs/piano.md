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

## Passo 3 — Confronto con il paper: fisiologia e movimento
*In sospeso: da chiedere alla prof alla call prima di farlo.*

**Obiettivo:** completare la replica con le altre righe della Tabella 4 del paper.

| esperimento | dati | compito |
|---|---|---|
| A | tutti i segnali | riposo vs calcolo + discorso + corsa |
| B | solo fisiologia | riposo vs calcolo + discorso + corsa |
| C | solo movimento | riposo vs calcolo + discorso + corsa |

- A, B, C si confrontano con la Tabella 4 del paper

**Fatto quando:** tabella "noi contro loro".

## Passo 4 — Feature dai dati grezzi
**Obiettivo:** il contributo nuovo del tirocinio. Finestre da 60 secondi.

Un segnale alla volta:
1. **sudore** da `bioz`: numero di reazioni, ampiezza, livello di fondo
2. **battito** dal PPG: variabilità del battito (SDNN, RMSSD, pNN50)
3. **movimento**: magnitudine
4. **temperatura**: media, dev. standard, pendenza

Per il PPG: misurare quanta parte del segnale è recuperabile, anche quella è un risultato.

**Fatto quando:** nuova tabella con queste feature.

## Passo 5 — Classificatori sulle nuove feature
**Obiettivo:** capire se le nostre feature aiutano.
- stesso codice del passo 2
- aggiungere un gruppo di feature alla volta
- ripetere gli esperimenti del passo 3

**Fatto quando:** si sa quali feature servono.

## Passo 6 — Rete neurale (MLP)
**Obiettivo:** confrontare una rete neurale con i classificatori classici.
- rete piccola, stessa validazione
- sulle tabelle dei passi 1 e 4

**Fatto quando:** risultato MLP accanto agli altri.

## Passo 7 — Conclusioni
- tabella finale con tutti i risultati
- discussione: quale modello, quali feature, stress o movimento?
- revisione della relazione

---

## Da confermare con la prof
- Il passo 3 (tutti / solo fisiologia / solo movimento) va fatto? Sono le altre righe della Tabella 4 del paper, quindi rientrerebbe nella replica
- Ha senso provare anche un compito **senza corsa** (riposo contro calcolo + discorso)? Nella corsa il movimento rende lo stress ovvio; togliendola si vedrebbe se il modello riconosce lo stress o solo il movimento
- Normalizzazione per persona: dichiararla come limite o provare una variante con il solo riposo (calibrazione)?
- `ddca342b` e `a5e823ad`: tenerle o escluderle dalla valutazione?
- Finestre da 60 s: rifare anche la tabella degli autori a 60 s?
- Tabella 2 o Tabella 4? (la 2 ha il titolo del binario ma probabilmente i numeri del ternario)

## Da provare, non urgente
- standardizzazione del sudore con la mediana invece della media (passo 4)
