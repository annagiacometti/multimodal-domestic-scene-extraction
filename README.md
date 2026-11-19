# Multimodal Domestic Scene Metadata Extraction (VLM Experiment)

Piccolo esperimento riproducibile di estrazione strutturata di metadati visivi per ambienti domestici e attività quotidiane tramite modelli Vision-Language open source.

## Metodologia e Strumenti
- **Modello:** `Qwen/Qwen2.5-VL-3B-Instruct` (eseguito su Google Colab con GPU T4 tramite libreria Hugging Face `transformers`).
- **Dataset:** Campione filtrato in streaming dal dataset aperto `HuggingFaceM4/COCO` (scene di interni domestici e oggetti di uso quotidiano).
- **Output:** Trasformazione dell'input visivo non strutturato in schede JSON normalizzate (`contesto_stanza`, `oggetti_principali`, `attivita_rilevata`, `stato_ambiente`, `suggerimento_supporto`).

## Contenuto del Repository
- `multimodal_domestic_scene_extraction.ipynb`: Notebook Colab riproducibile.
- `domestic_scene_vlm_results.csv`: Confronto tra didascalia di riferimento (*ground truth*) e output JSON generato dal modello.
