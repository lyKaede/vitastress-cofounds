# Piano di lavoro

**Regola:** prima si rifà il risultato degli autori, poi si cambia una cosa alla volta.
**Validazione, sempre la stessa:** addestro su 20 persone, provo sulla 21ª, a rotazione (leave-one-subject-out).
**Scrittura:** una o due pagine di relazione alla fine di ogni passo.

---

## Fatto
- Esplorazione e controllo del dataset
- Figura 4 riprodotta su 21 soggetti, differenze dal paper spiegate
- Feature usate dagli autori → `docs/feature_autori.md`

---

## Passo 1 — Rifare la loro tabella
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

## Passo 2 — Il loro classificatore binario
**Obiettivo:** avvicinarsi all'89% (Tabella 4).
- Random Forest, kNN, LDA, Decision Tree, Logistic Regression, XGBoost
- accuracy e F1 per ogni classificatore

**Fatto quando:** una tabella con i risultati di tutti e sei.

## Passo 3 — Confronto con il paper e confondente movimento
**Obiettivo:** capire se riproduciamo il paper e quanto pesa il movimento.

| esperimento | dati | compito |
|---|---|---|
| A | tutti i segnali | riposo vs calcolo + discorso + corsa |
| B | solo fisiologia | riposo vs calcolo + discorso + corsa |
| C | solo movimento | riposo vs calcolo + discorso + corsa |
| **D** *(nuovo)* | tutti i segnali | **riposo vs calcolo + discorso** (senza corsa) |
| **E** *(nuovo)* | solo movimento | **riposo vs calcolo + discorso** (senza corsa) |

- A, B, C si confrontano con le tabelle del paper
- D ed E dicono se il modello riconosce lo stress o il movimento: senza la corsa il movimento non può aiutare

**Fatto quando:** tabella "noi contro loro" + risultato senza corsa.

*→ Qui la relazione è già valida.*

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
- ripetere gli esperimenti A–E del passo 3

**Fatto quando:** si sa quali feature servono, **soprattutto nel compito senza corsa**.

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
- Tabella 2 o Tabella 4? (la 2 ha il titolo del binario ma probabilmente i numeri del ternario)

## Da provare, non urgente
- standardizzazione del sudore con la mediana invece della media (passo 4)
