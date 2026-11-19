# Multimodal Domestic Scene Metadata Extraction (VLM Experiment)

Esperimento riproducibile di estrazione strutturata di metadati visivi per ambienti domestici e attività quotidiane tramite modelli Vision-Language open source.

## Metodologia e Strumenti
- **Modello:** `Qwen/Qwen2.5-VL-3B-Instruct` (eseguito su Google Colab con GPU T4 tramite libreria Hugging Face `transformers` e decodifica deterministica `do_sample=False`).
- **Dataset:** Campione preliminare filtrato in streaming di **50 immagini** dal dataset aperto `lmms-lab/COCO-Caption` (split `val`, selezionate tramite filtro lessicale sulle didascalie inglesi con le parole chiave: `kitchen`, `living room`, `bedroom`, `table`, `couch`, `chair`, `sink`, `cup`, `bowl`, `book`).
- **Output:** Trasformazione dell'input visivo non strutturato in schede JSON strutturate (`contesto_stanza`, `oggetti_principali`, `attivita_rilevata`, `stato_ambiente`, `suggerimento_supporto`).
- **Nota sulla lingua:** Il prompt e l'output JSON sono stati impostati in italiano per simulare uno scenario di assistenza domestica per utenti italofoni e osservare il comportamento multilingue *zero-shot* del modello rispetto alle didascalie originali in inglese di COCO.

## Risultati Preliminari e Criteri di Valutazione

### 1. Validità sintattica JSON: `50/50`
Tutti e 50 gli output generati hanno superato il parsing automatico in Python (`json.loads`) senza errori di sintassi.

### 2. Riscontro lessicale diretto con le didascalie COCO: `45/50`
Il conteggio `45/50` è interamente calcolato in automatico tramite script Python nel notebook (`multimodal_domestic_scene_extraction.ipynb`), applicando questa regola riproducibile:
- **Regola di match:** Un campione è conteggiato come riscontrato (`1`) se **almeno una** parola chiave italiana estratta dal modello nei campi `contesto_stanza` o `oggetti_principali` trova almeno un equivalente inglese nella didascalia umana di riferimento (*ground truth*) di COCO (confronto case-insensitive su sottostringa).
- **Gestione bilingue (IT $\rightarrow$ EN):** Il confronto non avviene tra stringhe non tradotte, ma attraverso un dizionario esplicito di 18 coppie di equivalenze e sinonimi definito nel codice:
  - `cucina` $\rightarrow$ `["kitchen"]`
  - `soggiorno` $\rightarrow$ `["living room", "couch", "sofa"]`
  - `camera` $\rightarrow$ `["bedroom", "bed"]`
  - `bagno` $\rightarrow$ `["bathroom", "toilet", "sink"]`
  - `studio` $\rightarrow$ `["office", "desk", "computer", "laptop"]`
  - `tavolo` $\rightarrow$ `["table", "desk"]`
  - `sedia` $\rightarrow$ `["chair"]`
  - `divano` $\rightarrow$ `["couch", "sofa"]`
  - `tazza` $\rightarrow$ `["cup", "mug"]`
  - `ciotola` $\rightarrow$ `["bowl"]`
  - `libro` $\rightarrow$ `["book"]`
  - `fornello` $\rightarrow$ `["stove", "oven"]`
  - `frigorifero` $\rightarrow$ `["refrigerator", "fridge"]`
  - `lavandino` $\rightarrow$ `["sink"]`
  - `piatto` $\rightarrow$ `["plate", "dish"]`
  - `persona` $\rightarrow$ `["person", "man", "woman", "boy", "girl", "people"]`
  - `cane` $\rightarrow$ `["dog"]`
  - `gatto` $\rightarrow$ `["cat"]`
- **Limiti della metrica:** Si tratta di una verifica preliminare su campione ridotto (50 immagini), senza intervalli di confidenza né confronto con altri modelli, e misura la presenza di almeno un concetto condiviso (non l'esattezza di tutti gli oggetti elencati).

### 3. Ispezione manuale qualitativa (primi 10 campioni)
Oltre al calcolo automatico `45/50`, i primi 10 campioni sono stati ispezionati manualmente per osservare il comportamento semantico del modello al di là del semplice match lessicale:

| ID | Didascalia COCO (Ground Truth) | Stanza VLM | Oggetti principali VLM | Esito automatico e nota manuale |
|---|---|---|---|---|
| **0** | *A room with blue walls and a white sink and door.* | `bagno` | coperchio del vaso da bagno, candela, coppa, lavabo con rubinetto, portello | **Match** (`bagno`). Riconoscimento coerente del contesto. |
| **1** | *A bathroom with a toilet, sink, and shower.* | `bagno` | WC, lavandino, specchio, tenda, asciugamano | **Match** (`bagno`, `lavandino`). Corretta individuazione degli elementi principali. |
| **3** | *A bathroom sink with toiletries on the counter.* | `bagno` | cassettiera nera, cucchiaino, cucchiaino, cucchiaino, cucchiaino | **Match** (`bagno`). L'ispezione manuale evidenzia però un errore su oggetti piccoli (*toiletries* $\rightarrow$ *cucchiaino*) e la ripetizione dello stesso termine. |
| **5** | *A man in a wheelchair and another sitting on a bench that is overlooking the water.* | `altri` | bench, person, wheelchair, signboard, beach | **Non match**. Immagine esterna entrata nel campione per parola chiave; il modello riconosce gli oggetti ma effettua *code-switching* in inglese. |
| **6** | *A cute kitten is sitting in a dish on a table.* | `altra` | gatto bianco e nero seduto su un piatto rotondo, libri sparsi sul tavolo... | **Match** (`tavolo`, `piatto`). Riconoscimento corretto degli oggetti nonostante la stanza non classificata. |

## Limiti Osservati
- **Ripetizione lessicale:** Con decodifica deterministica (`do_sample=False`), il modello compatto (3B parametri) tende talvolta a ripetere termini identici nella lista `oggetti_principali` (es. campioni `3`, `4`, `8`, `9`).
- **Imprecisioni su dettagli secondari e *code-switching*:** Sporadiche attribuzioni errate su oggetti di piccole dimensioni in scene affollate o passaggio all'inglese in corrispondenza di scene esterne entrate nel campione per ambiguità delle parole chiave di filtro.

## Contenuto del Repository
- `multimodal_domestic_scene_extraction.ipynb`: Notebook Colab riproducibile con codice, dizionario bilingue di verifica e confronto qualitativo.
- `domestic_scene_vlm_results.csv`: Tabella comparativa completa su 50 campioni.
