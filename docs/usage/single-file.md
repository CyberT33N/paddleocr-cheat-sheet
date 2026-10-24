# Usage — Convert a Single PDF

Converts one PDF (or image) to Markdown + JSON + Word using the
[highest-accuracy model set](../models/highest-accuracy-models.md).

## Command

```shell
cd <PROJECT_DIR>
source .venv/bin/activate

python -m paddleocr pp_structurev3 \
  -i "<INPUT_FILE>" \
  --device gpu:0 \
  --layout_detection_model_name PP-DocLayout_plus-L \
  --text_detection_model_name PP-OCRv5_server_det \
  --text_recognition_model_name ch_SVTRv2_rec \
  --use_doc_orientation_classify True \
  --use_textline_orientation True \
  --save_path "<OUTPUT_DIR>"
```

## Flag by flag

| Flag | Value | Purpose |
|---|---|---|
| `-i` | `<INPUT_FILE>` | One PDF or image path (`.pdf`, `.jpg`, `.png`, ...) |
| `--device` | `gpu:0` | CUDA device 0 — replaces the CPU default |
| `--layout_detection_model_name` | `PP-DocLayout_plus-L` | Accuracy-first layout model (see [models](../models/highest-accuracy-models.md)) |
| `--text_detection_model_name` | `PP-OCRv5_server_det` | Accuracy-first text detector (see [models](../models/highest-accuracy-models.md)) |
| `--text_recognition_model_name` | `ch_SVTRv2_rec` | Challenge-winning recognizer; German alternative `PP-OCRv5_server_rec` (see [models](../models/highest-accuracy-models.md)) |
| `--use_doc_orientation_classify` | `True` | Auto-rotate scanned pages (0°/90°/180°/270°) |
| `--use_textline_orientation` | `True` | Fix inverted text lines |
| `--save_path` | `<OUTPUT_DIR>` | Target directory, created automatically |

## Output

For each input document, `<OUTPUT_DIR>` receives a subfolder named after the
document, containing per-page results:

```text
<OUTPUT_DIR>/
└── <document-name>/
    ├── ..._markdown_<page>.md    # Markdown (tables embedded as HTML)
    ├── ..._json_<page>.json      # structured result with coordinates + scores
    ├── ..._word_<page>.docx      # Word document (requires python-docx)
    └── imgs/                     # extracted page/region images
```

A multi-page PDF is processed page-by-page in one invocation.

## Notes

- First run downloads model weights (a few hundred MB total) — internet required once.
- If `--device gpu:0` fails, verify `CUDA: True` as described in
  [setup](../installation/setup.md), not the CPU fallback.
- For German-heavy documents with umlaut issues, swap the recognition model as
  described in the [models document](../models/highest-accuracy-models.md).

→ Next: [convert a whole folder](batch-folder.md) · [troubleshooting](troubleshooting.md)
