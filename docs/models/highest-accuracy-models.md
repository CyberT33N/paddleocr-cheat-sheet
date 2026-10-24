# Highest-Accuracy Model Set (PP-StructureV3)

`PP-StructureV3` is a **pipeline of specialized models** — one per
document-understanding task. Every stage can be overridden with a dedicated CLI
flag. The table below lists the recommended **maximum-accuracy** model per stage.
This is the exact set used by all usage documents in this cheat sheet.

## Recommended accuracy-first set

| Pipeline stage | CLI flag | Highest-accuracy model | Why this model |
|---|---|---|---|
| Layout detection | `--layout_detection_model_name` | `PP-DocLayout_plus-L` | Large variant; most accurate region localization (tables, text blocks, figures) |
| Text detection | `--text_detection_model_name` | `PP-OCRv5_server_det` | Server-grade detector; finds small/dense text better than the mobile variant |
| Text recognition | `--text_recognition_model_name` | `ch_SVTRv2_rec` | Winner of the PaddleOCR Model Challenge (end-to-end recognition), ≈6 pp above PP-OCRv4 on benchmark A |
| Text recognition (German alt.) | `--text_recognition_model_name` | `PP-OCRv5_server_rec` | Multilingual server model — prefer for German/European documents with umlauts/ß |
| Document orientation | `--doc_orientation_classify_model_name` | `PP-LCNet_x1_0_doc_ori` *(default)* | Classifies 0°/90°/180°/270° page rotation |
| Page dewarping | `--doc_unwarping_model_name` | `UVDoc` *(default)* | Flattens photos of curved/warped pages |
| Text-line orientation | `--textline_orientation_model_name` | `PP-LCNet_x1_0_textline_ori` *(default)* | Fixes upside-down single lines |
| Wired table structure | `--wired_table_structure_recognition_model_name` | `SLANeXt_wired` *(default)* | Highest-accuracy reconstruction of bordered tables |
| Wireless table structure | `--wireless_table_structure_recognition_model_name` | `SLANet_plus` *(default)* | Highest-accuracy reconstruction of borderless tables |
| Table cell detection | `--wired_table_cells_detection_model_name` / `--wireless_table_cells_detection_model_name` | `RT-DETR-L_wired_table_cell_det` / `RT-DETR-L_wireless_table_cell_det` *(default)* | Large RT-DETR cell detectors |
| Formula recognition | `--formula_recognition_model_name` | `PP-FormulaNet_plus-L` *(default)* | Large formula model, outputs LaTeX |

The defaults for the last rows are already the accuracy-first choices in
`paddleocr 3.7.x`. The usage commands therefore override only the three stages
with the biggest quality impact: **layout, text detection, text recognition**.

## German-language note

`ch_SVTRv2_rec` is trained Chinese + English. It still reads digits and table
structures in German documents extremely well (verified on German payslips), but
if you see umlaut/ß problems, switch recognition to `PP-OCRv5_server_rec` — same
flag, same accuracy class, multilingual vocabulary.

## Full model list

Every stage flag accepts any model name from the official tables in the PaddleOCR
documentation:
<https://www.paddleocr.ai/latest/en/version3.x/pipeline_usage/PP-StructureV3.html>
(section "Model list"). Weights are downloaded on first use to
`~/.paddlex/official_models/<MODEL_NAME>/` and cached afterwards.

## Speed vs. accuracy

On a 24 GB GPU (e.g. RTX 4090) the accuracy-first set runs comfortably fast.
For edge CPUs, swap to the mobile variants (`PP-DocLayout_plus-S`,
`PP-OCRv5_mobile_det`, `PP-OCRv5_mobile_rec`) at the same flags.

→ Ready-to-run commands: [single file](../usage/single-file.md) ·
[batch folder](../usage/batch-folder.md)
