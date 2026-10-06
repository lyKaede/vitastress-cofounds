# Stress o movimento? Riconoscimento dello stress da segnali wearable

Tirocinio · Interfacce uomo-macchina · Università degli Studi dell'Insubria · 2026

**Stato:** in corso

## Il progetto

I modelli che riconoscono lo stress da dispositivi indossabili riportano buone accuratezze,
ma su VitaStress un modello che usa solo l'accelerometro funziona quanto uno che usa tutti i segnali.
Il dubbio è che i modelli stiano riconoscendo il movimento più che lo stress.

Questo progetto:
- ricostruisce la pipeline partendo dai segnali grezzi del braccialetto (PPG, conduttanza cutanea, accelerometro)
- confronta modelli addestrati solo sul movimento, solo sulla fisiologia e su entrambi
- valuta un task che esclude la condizione di sforzo fisico
- confronta classificatori classici e una piccola rete neurale, con validazione leave-one-subject-out

## Dataset

[VitaStress](https://github.com/paulvincenz/VitaStress), Schreiber et al., ICMI 2025.
21 partecipanti, braccialetto Corsano CardioWatch, quattro condizioni: riposo, stress cognitivo,
stress socio-valutativo (public speaking), sforzo fisico.

I dati **non sono inclusi** in questa repository. Per scaricarli:

```
git clone https://github.com/paulvincenz/VitaStress.git
```

Licenza del dataset: CC BY-NC 4.0.

## Struttura

```
notebooks/   notebook Colab, in ordine di esecuzione
src/         funzioni riutilizzate dai notebook
figures/     grafici prodotti
docs/        diario di lavoro e decisioni prese
```

## Avanzamento

- [x] Esplorazione del dataset
- [ ] Riproduzione dei grafici del paper (Figura 3, Figura 4)
- [ ] Preprocessing e finestre
- [ ] Estrazione delle feature
- [ ] Baseline classiche
- [ ] Analisi dei confondenti
- [ ] Rete neurale
- [ ] Relazione

## Come eseguire

I notebook sono pensati per Google Colab. Il primo notebook scarica il dataset automaticamente.

## Riferimenti

- Schreiber P., Burbach S., Cinar B., Mackert L., Maleshkova M. *VitaStress: A Multimodal Dataset for Stress Detection.* ICMI 2025.
