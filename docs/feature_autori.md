# Feature usate dagli autori

**Domanda della prof:** che feature hanno usato gli autori? Solo i valori dello smartwatch, o hanno ricavato altre feature dai dati grezzi?

**Risposta breve:** per battito, sudore e temperatura usano i **valori già calcolati dal braccialetto**. Le uniche feature calcolate da loro sono **media, deviazione standard e varianza dell'accelerometro**. I segnali grezzi fisiologici (PPG e `bioz`) non li usano.

**Fonte:** Schreiber et al., *Stress Detection from Multimodal Wearable Sensor Data*, arXiv 2508.10468, sezione 3.6 "Classification Algorithms" (p. 8) e sezione 4.1.2 (PCA).

---

## Cosa scrivono

- *"we used the preprocessed data provided by the Corsano platform"*: usano i dati già elaborati dal braccialetto.
- Le modalità combinate sono sei: battito, intervalli fra battiti (RR), respirazione, conduttanza della pelle, temperatura, accelerometro.
- *"each feature is resampled to have a sampling frequency of 1/30 seconds"*: ogni misura viene portata a **un valore ogni 30 secondi**.
- Intervalli RR e respirazione vengono **eliminati** perché *"they contain a lot of missing data"*.
- *"the physiological values of each subject are normalized"*: **normalizzazione per persona**.
- Per la PCA (sez. 4.1.2) usano: media, deviazione standard e varianza di accX, accY, accZ, più conduttanza della pelle, battito e temperatura.
- Nei risultati dicono che le feature di movimento sono quelle *"subject to the most pre-processing and aggregation"*.

## Tabella riassuntiva

| segnale | cosa usano | file | calcolato da loro? |
|---|---|---|---|
| battito | `bpm` | `activity` | no, valore del braccialetto |
| sudore | `stress_skin` | `emography` | no, valore del braccialetto |
| temperatura | `temp_sk1` | `temperature` | no, valore del braccialetto |
| movimento | media, dev. standard, varianza di X, Y, Z | `acc` | **sì**, dall'accelerometro grezzo |
| intervalli RR | — | `rr_interval` | eliminati (troppi dati mancanti) |
| respirazione | — | `activity` | eliminata (troppi dati mancanti) |

Ogni riga del loro dataset è un campione da **30 secondi**.

## Dati grezzi ed elaborati: una precisazione

Il paper (p. 12) dice che `activity.csv` contiene i dati elaborati e *"the other files contain the raw data from the sensors"*. Guardando i file, non è del tutto esatto: anche `emography`, `temperature` e `rr_interval` sono valori calcolati dal braccialetto.

| tipo | file | contenuto | misure al secondo |
|---|---|---|---|
| **grezzo** | `ppg2_green_6`, `ppg2_red_182`, `ppg2_infra_red_22`, `ppg3` | PPG, da cui si ricava il battito | 62,5 / 32 |
| **grezzo** | `bioz` | conduttanza della pelle (unità da 100 pS) | 25 |
| **grezzo** | `acc` | accelerometro | 32 |
| quasi grezzo | `heat_flux_sensor_temperature` | temperatura della pelle | 1 |
| elaborato | `activity` | battito, respirazione, ecc. | 1 ogni 30 s |
| elaborato | `emography` | indice di sudore `stress_skin` | 1 ogni 60 s |
| elaborato | `temperature` | `temp_sk1` | 1 ogni 30 s |
| elaborato | `rr_interval` | intervalli fra battiti | irregolare |

## Cosa non usano

- il **PPG grezzo**: da qui si può calcolare la variabilità del battito, che il braccialetto perde quasi sempre durante lo stress
- **`bioz`**: da qui si possono contare le singole reazioni di sudore
- nessuna feature calcolata su finestre di tempo per i segnali fisiologici

Lo riconoscono loro stessi nelle conclusioni: includere i dati grezzi, campionati a frequenza più alta, dovrebbe migliorare l'analisi.

## Conseguenza per il progetto

Lo spazio per il contributo nuovo sono i segnali grezzi. Feature proposte, da aggiungere un gruppo alla volta:

| segnale grezzo | feature | origine |
|---|---|---|
| `bioz` | numero di reazioni, intervallo medio, Δ ampiezza, livello di fondo e pendenza | progetto d'esame (GSR) + letteratura |
| PPG | numero di battiti, intervallo medio, SDNN, RMSSD, pNN50 | progetto d'esame (ECG) + letteratura (WESAD) |
| `acc` | magnitudine del movimento | letteratura |
| temperatura (1 Hz) | media, deviazione standard, pendenza | progetto d'esame (std) + letteratura |

Per le feature sul battito servono finestre da almeno **60 secondi**: in 30 secondi ci sono troppo pochi battiti.
