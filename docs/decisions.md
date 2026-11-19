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

**28. Permutation importance** accanto alla MDI: più affidabile per dire quanto conta davvero un gruppo di feature. Fatta il 16 novembre (notebook 03, Fase 20): un gruppo intero mescolato sulla persona esclusa, 5 ripetizioni.

**29. Domanda di ricerca:** distinguere lo stress psicologico dal riposo e dallo stress fisico senza il movimento; ogni esperimento con e senza accelerometro.

**30. Solo le 4 fasi principali** (riposo iniziale, calcolo, discorso, corsa): sono i momenti in cui sappiamo con certezza cosa sta facendo la persona. Pause, preparazione del discorso e recupero restano fuori. La prova con tutte le fasi (RF 0,80, vicino allo 0,77 del paper) va nella relazione come probabile spiegazione della differenza.

## Aggiornamenti del 16 novembre

**31. Pulsanti premuti più volte: l'ultimo inizio che ha una fine dopo di sé, con la prima fine successiva.** Prima si prendevano il primo inizio e la prima fine. Per `3f27501c` il calcolo preso era un tentativo interrotto di 2 minuti invece del calcolo vero di 5 minuti (con gli errori annotati). La regola «ultima fine» del collega da noi non va: togliendo le parentesi dai nomi, `7bb4dafd` avrebbe un riposo lungo un'ora. Se la fine non ha orario (`7bb4dafd`, calcolo) la fase viene saltata con un avviso. Effetto: 1.165 → 1.170 righe; RF 0,93 (con la tabella vecchia, 5 semi diversi davano 0,93–0,94: lo 0,94 era un seme fortunato).

**32. MLP: solo scikit-learn, una configurazione.** `MLPClassifier(hidden_layer_sizes=(64, 32), max_iter=1000, early_stopping=True, random_state=42)`, senza prove di architetture e senza PyTorch. Serve a dire se il limite sta nel modello o nelle feature: con le 12 feature degli autori 0,86 contro 0,93 di Random Forest. Si riprova una volta con le feature nuove. PyTorch solo se si fa la rete profonda.

**33. Parte 6 nel notebook 03:** le fasi intermedie si leggono solo per spiegare la differenza con il paper (pause e recupero = riposo, preparazione = stress; tutte le pause di una persona). Con tutto RF 0,81 (paper 0,77). Il lavoro resta sulle 4 fasi principali (decisione 30).
## Feature nuove (17 novembre)

**34. Magnitudine del movimento come misura principale del movimento nelle nostre prove.** √(accX² + accY² + accZ²), media e deviazione standard ogni 30 s. Non dipende da come è girato il braccialetto (a riposo `ddca342b` ha 505 come gli altri, 505–527, senza correzioni) e non contiene la postura: misura solo quanto ci si muove. Le 9 colonne degli autori restano solo per il confronto con il paper. Ogni prova «con movimento» si può fare nelle due versioni; quella principale è con la magnitudine.

**35. `temp_sk1` sostituita dalla temperatura della pelle.** `temp_sk1` coincide con `cbt` (differenza media 0,001 °C), la temperatura corporea interna stimata dal braccialetto: è quasi costante (37,2 °C in tutte le fasi) e per questo non serviva. Dal file `heat_flux_sensor_temperature` (1 Hz): `skin_temp` (media, deviazione standard, pendenza in °C al minuto) e `heatflux` (media). Valori della pelle sotto 15 °C → mancanti (nessuno nelle nostre righe).

**36. Feature nuove sugli stessi blocchi da 30 s del notebook 03** (stesse 1.170 righe), così i risultati si confrontano direttamente. La finestra da 60 s si decide quando serve (variabilità del battito dal PPG).

**37. Controllo del riposo come prima fase.** Il riposo è sempre all'inizio, con il braccialetto appena messo che si scalda: una feature che dipende dal tempo può riconoscere «inizio dell'esperimento». Per ogni feature nuova legata al tempo si controlla anche il confronto stress cognitivo contro stress fisico, senza riposo.

**38. Sudore: il segnale grezzo `bioz` invece del solo indice `stress_skin`.** `bioz` è la conduttanza della pelle a 25 Hz (sale quando si suda). In logaritmo, così una variazione dell'1% conta uguale per tutti; per ogni blocco da 30 s: livello (mediana mobile su 20 s), pendenza, numero di reazioni (picchi con prominenza ≥ 0,01, cioè circa 1%) e ampiezza totale. Soglia provata fra 0,5% e 5%: nessuna differenza (ternario 0,80–0,81, cognitivo contro fisico 0,77–0,78). Da solo l'indice `stress_skin` fa 0,36 (quasi il caso), il segnale grezzo 0,61. `stress_skin` resta nella fisiologia per non togliere niente rispetto agli autori.

**39. Righe senza livello del sudore tolte** (3 blocchi di riposo all'inizio, segnale insufficiente): da 1.170 a 1.167. Il riferimento «fisiologia degli autori» passa da 0,74 a 0,75.

**40. Variabilità del battito (PPG): documentata, non entra nel modello.** Il battito si vede bene nel PPG del polso quasi solo a riposo: finestre da 10 s con il battito visibile (più di metà dell'energia fra 0,5 e 4 Hz alla frequenza del cuore) riposo 71% · calcolo 15% · discorso 4% · corsa 3%, con risultati simili su tutti i canali (verde, rosso, infrarosso, `ppg3`). Anche gli intervalli del braccialetto (`rr_interval`) coprono il 85% del riposo, il 32% del calcolo, l'11% del discorso e lo 0% della corsa. Una colonna di variabilità del battito sarebbe piena a riposo e vuota o sbagliata sotto stress: il modello imparerebbe la qualità del segnale (cioè il movimento), non lo stress. È un limite del braccialetto da polso, da dichiarare nella tesi; spiega anche perché gli autori hanno scartato gli intervalli RR. Decisione mia, 19 novembre.

**41. MLP con le feature nuove: in attesa.** Decisione mia, 19 novembre.

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
