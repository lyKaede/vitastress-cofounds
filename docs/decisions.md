# Decisioni

Ogni scelta importante: cosa ho deciso e perché.

## Pulizia dei dati

**1. Battito pari a 0 → dato mancante.** Un battito zero è impossibile: errore del sensore.

**2. Temperatura pari a 0 °C → dato mancante.** Errore del sensore (una persona, fase cognitive). Nel paper non è stato tolto e produce la coda a −8 nella Figura 4.

**3. Zeri di `stress_skin` tenuti.** È un indice calcolato dal braccialetto: lo zero significa "nessuna reazione", non è un errore.

**4. Persona `a5e823ad`: riposo escluso.** Dura 82 minuti contro i ~10 degli altri, probabile pulsante premuto male. Le fasi più corte di 0 o più lunghe di 30 minuti vengono scartate.

**5. Righe con dati mancanti tolte (25 su 1.170).** Sono poche; così il "dato mancante" non può diventare un indizio per il modello.

## Figura 4

**6. Violin plot con `cut=0`.** Il violino si ferma ai valori osservati, senza code inventate.

**7. Accelerometro: media ogni 30 secondi.** Stesso ritmo delle altre misure; il paper parla di media dell'accelerometro.

**8. Standardizzazione per persona.** Ogni persona confrontata con sé stessa. Il paper dichiara lo z-score ma non dice se per persona o globale. Effetto collaterale: su `stress_skin`, pieno di zeri, appiattisce le differenze fra fasi.

## Tabella e classificatore (passi 1-2)

**9. Un campione ogni 30 secondi.** Come gli autori.

**10. Accelerometro: media, deviazione standard e varianza per blocco.** Come gli autori (sez. 4.1.2).

**11. Sudore da un valore ogni 60 s a uno ogni 30 s,** ripetendo il valore precedente (`ffill(limit=1)`).

**12. Binario: riposo = 0; calcolo, discorso e corsa = 1.** Come il paper.

**13. Validazione leave-one-subject-out.** Come gli autori; misura il modello su persone mai viste.

**14. Iperparametri standard, senza grid search.** Il paper non dichiara i valori provati.

**15. F1 macro.** Come il paper; adatta a classi sbilanciate (65% stress, 35% riposo).

**16. `random_state=42`.** Risultati riproducibili.

**17. Riferimento: Tabella 4 del paper (89%).** La Tabella 2 ha il titolo del binario ma i numeri del ternario.

## Le due persone difficili

**18. `ddca342b`: braccialetto orientato al contrario.** A riposo è l'unica persona con asse X positivo (+442) e asse Z negativo (−106); per le altre 20 X va da −502 a −68 e Z da +79 a +492. La gravità misurata è normale (462), quindi il sensore funziona. Il modello sbaglia 18 riposi su 20 perché usa la postura del polso. Verifica (addestrando sulle altre 20): senza le medie dell'accelerometro 0,75, con solo la fisiologia 0,91. Per ora lasciata così; la magnitudine del movimento (passo 4) non dipende dall'orientamento.

**19. `a5e823ad`: errori dovuti alla normalizzazione.** Senza riposo, la media della persona è calcolata solo sulle fasi di stress: metà delle righe di stress finisce sotto la media e viene scambiata per riposo (7 errori su 11 nel calcolo, 5 su 14 nella corsa). Con solo la fisiologia resta bassa (0,69). Rispondere sempre «stress» su questa persona darebbe 1,00: il suo risultato non è confrontabile.

## Ternario (call del 6 novembre)

**20. Binario chiuso, si continua sul ternario.** Decisione della prof. Il binario resta come replica riuscita.

**21. Classi del ternario come il paper:** riposo / calcolo + discorso (stress cognitivo e sociale) / corsa (stress fisico).

**22. `a5e823ad` in sospeso, esclusa per ora.** Nel ternario non avrebbe righe della classe riposo. Interruttore `ESCLUDI` nella Fase 0 del notebook 03. Con lei inclusa RF scende da 0,91 a 0,88.

**23. Ablation per gruppi di feature, non per singola feature.** Feature collegate (std e varianza dello stesso asse) si sostituiscono a vicenda: togliendone una sola il risultato non cambia e si concluderebbe a torto che non serve. Cinque gruppi: battito, sudore, temperatura, postura (medie acc), agitazione (std e varianza acc).

**24. Riferimento minimo del ternario:** rispondere sempre la classe più numerosa (0,36).

**25. MLP con scikit-learn** (`MLPClassifier`, strati 64 e 32, early stopping, random_state 42). Rete profonda sui segnali grezzi solo come ultimo capitolo opzionale.

## Aggiornamenti del 7 novembre (dal lavoro del collega)

**26. `ddca342b`: invertire gli assi Y e Z** (media di accY e accZ × −1 prima della normalizzazione). Corregge il braccialetto ruotato di 180° attorno a X. Sul ternario ddca342b passa da 0,49 a 0,98. Sostituisce l'ipotesi precedente «X e Z». Applicata nel notebook 03 (`ASSI_GIRATI`).

**27. `a5e823ad`: tenere gli ultimi 10 minuti del riposo** (il riposo annotato dura 82 minuti, gli altri circa 10). Sostituisce l'esclusione (decisione 22). Applicata nel notebook 03 (Fase 1).

**28. Permutation importance** accanto alla MDI: più affidabile per dire quanto conta davvero un gruppo di feature.

**29. Domanda di ricerca:** distinguere lo stress psicologico dal riposo e dallo stress fisico senza il movimento; ogni esperimento con e senza accelerometro.

**30. Solo le 4 fasi principali** (riposo iniziale, calcolo, discorso, corsa): sono i momenti in cui sappiamo con certezza cosa sta facendo la persona. Pause, preparazione del discorso e recupero restano fuori. La prova con tutte le fasi (RF 0,80, vicino allo 0,77 del paper) va nella relazione come probabile spiegazione della differenza.

## Da decidere

- Fase di preparazione del discorso: conta come stress? (per ora esclusa)
- Standardizzazione del sudore con la mediana invece della media (passo 4)
- Passo 3 (confronto fisiologia / movimento): da chiedere alla prof prima di procedere
- Compito senza corsa (riposo contro calcolo + discorso): da proporre alla prof come domanda
- `ddca342b` e `a5e823ad`: tenerle o escluderle dalla valutazione (da chiedere alla prof)
- Normalizzazione per persona: limite dichiarato o variante con il solo riposo (calibrazione)
- Finestre da 60 s per le feature del battito

## Note di metodo

- La normalizzazione per persona usa tutte le righe della persona, compresa quella di prova. Non usa le etichette ed è la scelta degli autori, ma va dichiarata.
