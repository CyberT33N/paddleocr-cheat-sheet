# Usage — Convert a Whole Folder (Batch)

## Option A — pass the directory to `-i` (recommended)

The batch sampler walks the directory **recursively** and picks up every `.pdf`
and image file automatically (verified in `paddlex`'s
`ImageBatchSampler._get_files_list`, paddleocr 3.7.0). One process, one model
load — maximum throughput.

```shell
cd <PROJECT_DIR>
source .venv/bin/activate

python -m paddleocr pp_structurev3 \
  -i "<INPUT_DIR>/" \
  --device gpu:0 \
  --layout_detection_model_name PP-DocLayout_plus-L \
  --text_detection_model_name PP-OCRv5_server_det \
  --text_recognition_model_name ch_SVTRv2_rec \
  --use_doc_orientation_classify True \
  --use_textline_orientation True \
  --save_path "<OUTPUT_DIR>"
```

Files are processed in sorted order; results land in one subfolder per document
under `<OUTPUT_DIR>`. Model flags: see
[highest-accuracy model set](../models/highest-accuracy-models.md).

## Option B — per-file loop (crash isolation)

Each PDF runs in its own process. Slower (models reloaded per file), but one
broken PDF cannot kill the rest of the batch:

```shell
cd <PROJECT_DIR>
source .venv/bin/activate

for pdf in "<INPUT_DIR>"/*.pdf; do
  echo ">>> $pdf"
  python -m paddleocr pp_structurev3 \
    -i "$pdf" \
    --device gpu:0 \
    --layout_detection_model_name PP-DocLayout_plus-L \
    --text_detection_model_name PP-OCRv5_server_det \
    --text_recognition_model_name ch_SVTRv2_rec \
    --use_doc_orientation_classify True \
    --use_textline_orientation True \
    --save_path "<OUTPUT_DIR>"
done
```

Keep the quotes around `"$pdf"` — file names with spaces work correctly.

## Which option?

| Situation | Choose |
|---|---|
| Normal batch, want maximum throughput | Option A |
| Mixed-quality PDFs, want per-file logs / isolation | Option B |
| PDFs nested in subfolders | Option A (recursive walk) |

→ [troubleshooting](troubleshooting.md)
