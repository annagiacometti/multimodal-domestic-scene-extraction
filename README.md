# Multimodal Domestic Scene Metadata Extraction (VLM Experiment)

Esperimento riproducibile di estrazione strutturata di metadati visivi per ambienti domestici e attività quotidiane tramite modelli Vision-Language open source.

## Metodologia e Strumenti
- **Modello:** `Qwen/Qwen2.5-VL-3B-Instruct` (eseguito su Google Colab con GPU T4 tramite libreria Hugging Face `transformers` e decodifica deterministica `do_sample=False`).
- **Dataset:** Campione preliminare filtrato in streaming di **50 immagini** dal dataset aperto `lmms-lab/COCO-Caption` (split `val`, selezionate tramite parole chiave legate ad ambienti interni domestici e oggetti di uso quotidiano).
- **Output:** Trasformazione dell'input visivo non strutturato in schede JSON strutturate (`contesto_stanza`, `oggetti_principali`, `attivita_rilevata`, `stato_ambiente`, `suggerimento_supporto`).
- **Nota sulla lingua:** Il prompt e l'output JSON sono in italiano per simulare uno scenario di assistenza domestica per utenti italofoni e osservare il comportamento multilingue *zero-shot* del modello rispetto alle didascalie originali in inglese di COCO.

## Risultati Preliminari e Criteri di Valutazione
- **Validità sintattica JSON (`50/50`):** Tutti e 50 gli output generati hanno superato il parsing automatico (`json.loads`) senza errori sintattici.
- **Riscontro lessicale diretto (`45/50`):**
  - *Criterio adottato:* Un campione è conteggiato come riscontrato se **almeno un elemento** estratto dal modello nei campi `contesto_stanza` o `oggetti_principali` trova corrispondenza diretta nella didascalia umana di riferimento (*ground truth*).
  - *Gestione bilingue (IT/EN):* Poiché gli output del VLM sono in italiano e le didascalie COCO in inglese, il confronto automatico nel notebook utilizza un dizionario controllato di equivalenze e sinonimi IT $\rightarrow$ EN (es. `divano` $\rightarrow$ `couch, sofa`; `tazza` $\rightarrow$ `cup, mug`; `cucina` $\rightarrow$ `kitchen`). Si tratta di una misura preliminare su campione ridotto, senza confronto con altri modelli.

### Esempi di confronto (dal campione analizzato)

| ID | Didascalia COCO (Ground Truth) | Stanza VLM | Oggetti principali VLM | Esito confronto |
|---|---|---|---|---|
| **0** | *A room with blue walls and a white sink and door.* | `bagno` | coperchio del vaso da bagno, candela, coppa, lavabo con rubinetto, portello | **Match** (`bagno`) |
| **1** | *A bathroom with a toilet, sink, and shower.* | `bagno` | WC, lavandino, specchio, tenda, asciugamano | **Match** (`bagno`, `lavandino`) |
| **3** | *A bathroom sink with toiletries on the counter.* | `bagno` | cassettiera nera, cucchiaino, cucchiaino, cucchiaino, cucchiaino | **Match parziale** (`bagno`; errore su oggetti piccoli e ripetizione) |
| **5** | *A man in a wheelchair and another sitting on a bench that is overlooking the water.* | `altri` | bench, person, wheelchair, signboard, beach | **Non match** (scena esterna filtrata per ambiguità lessicale; *code-switching* in inglese) |
| **6** | *A cute kitten is sitting in a dish on a table.* | `altra` | gatto bianco e nero seduto su un piatto rotondo, libri sparsi sul tavolo... | **Match** (`tavolo`, `piatto`) |

## Limiti Osservati
- **Ripetizione lessicale:** Con decodifica deterministica (`do_sample=False`), il modello compatto (3B parametri) tende talvolta a ripetere termini identici nella lista `oggetti_principali` (es. campioni `3`, `4`, `8`, `9`).
- **Imprecisioni su dettagli secondari e *code-switching*:** Sporadiche attribuzioni errate su oggetti di piccole dimensioni in scene affollate o passaggio all'inglese in corrispondenza di scene esterne entrate nel campione per ambiguità delle parole chiave di filtro.

## Contenuto del Repository
- `multimodal_domestic_scene_extraction.ipynb`: Notebook Colab riproducibile con codice, dizionario di verifica e confronto qualitativo.
- `domestic_scene_vlm_results.csv`: Tabella comparativa completa su 50 campioni.
