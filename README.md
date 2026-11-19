# Multimodal Domestic Scene Metadata Extraction (VLM Experiment)

Esperimento riproducibile di estrazione strutturata di metadati visivi per ambienti domestici e attività quotidiane tramite modelli Vision-Language open source.

## Metodologia e Strumenti
- **Modello:** `Qwen/Qwen2.5-VL-3B-Instruct` (eseguito su Google Colab con GPU T4 tramite libreria Hugging Face `transformers` e decodifica deterministica `do_sample=False`).
- **Dataset:** Campione filtrato in streaming di **50 immagini** dal dataset aperto `lmms-lab/COCO-Caption` (split `val`, selezionate tramite parole chiave legate ad ambienti interni domestici e oggetti di uso quotidiano).
- **Output:** Trasformazione dell'input visivo non strutturato in schede JSON normalizzate (`contesto_stanza`, `oggetti_principali`, `attivita_rilevata`, `stato_ambiente`, `suggerimento_supporto`).
- **Nota sulla lingua di output:** Il prompt e lo schema JSON sono stati impostati in italiano per simulare uno scenario applicativo di assistenza domestica in lingua italiana e osservare il comportamento multilingue *zero-shot* del modello rispetto alle didascalie originali in inglese di COCO.

## Risultati e Analisi Qualitativa
- **Validità sintattica JSON:** **100% (50/50)** degli output generati è risultato sintatticamente valido al parsing automatico (`json.loads`).
- **Coerenza semantica con le annotazioni COCO:** Il confronto lessicale tra le categorie estratte dal VLM e le didascalie umane di riferimento (*ground truth*) ha evidenziato un riscontro diretto su **45/50 campioni (90%)**.
- **Limiti osservati:** L'ispezione manuale degli output ha mostrato che il modello compatto (3B parametri) con decodifica deterministica (`do_sample=False`) tende occasionalmente a ripetere voci identiche nella lista `oggetti_principali` (es. ripetizione di *"cucina"* o *"cucchiaino"*), a generare lievi imprecisioni su oggetti piccoli in ambienti affollati o sporadici fenomeni di *code-switching* verso l'inglese, oltre a risentire della presenza di immagini esterne filtrate per ambiguità lessicale.

## Contenuto del Repository
- `multimodal_domestic_scene_extraction.ipynb`: Notebook Colab riproducibile con codice, metriche di verifica e confronto qualitativo.
- `domestic_scene_vlm_results.csv`: Tabella comparativa completa su 50 campioni.
