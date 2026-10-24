# PaddleOCR Cheat Sheet — PDF to Structured Markdown

Quick, copy-paste reference for converting PDFs and document images into structured
output (Markdown, JSON, Word) using the [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR)
`PP-StructureV3` pipeline on an NVIDIA GPU, with the highest-accuracy model set.

All commands are verified end-to-end against `paddleocr 3.7.0` /
`paddlepaddle-gpu 3.2.0` on Python 3.12 (RTX 4090).

## Table of Contents

| # | Topic | Document |
|---|-------|----------|
| 1 | Installation & verification | [docs/installation/setup.md](docs/installation/setup.md) |
| 2 | Highest-accuracy model list | [docs/models/highest-accuracy-models.md](docs/models/highest-accuracy-models.md) |
| 3 | Usage — convert a single PDF | [docs/usage/single-file.md](docs/usage/single-file.md) |
| 4 | Usage — convert a whole folder (batch) | [docs/usage/batch-folder.md](docs/usage/batch-folder.md) |
| 5 | Troubleshooting | [docs/usage/troubleshooting.md](docs/usage/troubleshooting.md) |

## Placeholders Used Throughout

This repository is public — every command is sanitized and machine-independent.
Replace the placeholders before running anything:

| Placeholder | Meaning | Example |
|---|---|---|
| `<PROJECT_DIR>` | Directory containing the Python venv | `/home/example-user/tools/paddleocr` |
| `<INPUT_FILE>` | Path to one PDF or image | `/home/example-user/documents/invoice-2025-03.pdf` |
| `<INPUT_DIR>` | Directory containing PDFs/images | `/home/example-user/documents/invoices/` |
| `<OUTPUT_DIR>` | Directory for converted results | `/home/example-user/documents/converted/` |

## One-Minute TL;DR

```shell
cd <PROJECT_DIR>
source .venv/bin/activate

python -m paddleocr pp_structurev3 \
  -i "<INPUT_DIR>" \
  --device gpu:0 \
  --layout_detection_model_name PP-DocLayout_plus-L \
  --text_detection_model_name PP-OCRv5_server_det \
  --text_recognition_model_name ch_SVTRv2_rec \
  --use_doc_orientation_classify True \
  --use_textline_orientation True \
  --save_path "<OUTPUT_DIR>"
```

Why these exact models: [docs/models/highest-accuracy-models.md](docs/models/highest-accuracy-models.md)
