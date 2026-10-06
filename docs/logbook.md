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

**Prossimo passo**
- Estendere la Figura 4 a tutte le 21 persone, con valori standardizzati
- Riprodurre la Figura 3 (dati mancanti)
