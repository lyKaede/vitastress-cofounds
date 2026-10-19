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

## Da decidere

- Fase di preparazione del discorso: conta come stress? (per ora esclusa)
- Standardizzazione del sudore con la mediana invece della media (passo 4)
- Compito senza corsa (riposo contro calcolo + discorso): in attesa della risposta della prof
- Persona `a5e823ad`: accuracy 0,63, da capire se dipende dall'assenza del riposo

## Note di metodo

- La normalizzazione per persona usa tutte le righe della persona, compresa quella di prova. Non usa le etichette ed è la scelta degli autori, ma va dichiarata.
