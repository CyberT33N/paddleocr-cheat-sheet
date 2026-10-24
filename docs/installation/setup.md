# Installation

Verified end-to-end on Linux (Python 3.12, NVIDIA RTX 4090). The steps are
distro-independent — only venv creation and pip commands matter.

## Prerequisites

- **Python** 3.10–3.12 (`python3 --version`)
- **NVIDIA GPU** with a driver that supports CUDA 12.x — check with `nvidia-smi`
  (the "CUDA Version" shown there is the maximum supported by the driver, not
  necessarily what is installed — the pip wheels bundle their own CUDA runtime)
- **Internet access** on first run: model weights are downloaded automatically to
  `~/.paddlex/official_models/` and cached; later runs work offline

> If you also use Conda, make sure you are NOT running inside the Conda `base`
> environment. After activating the venv, `which python` must point to
> `<PROJECT_DIR>/.venv/bin/python`. See [troubleshooting](../usage/troubleshooting.md).

## 1. Create the project directory and virtual environment

```shell
mkdir -p <PROJECT_DIR>
cd <PROJECT_DIR>

python3 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip
```

⚠️ **Never move or rename the project folder after creating the venv.** A venv
hard-codes its own absolute paths (shebangs, `pyvenv.cfg`, `VIRTUAL_ENV`).
Moving it breaks every entry point with `bad interpreter: ... no such file or
directory`. If you must relocate, delete `.venv` and recreate it at the new path.

## 2. Install the PaddlePaddle GPU runtime

```shell
python -m pip install paddlepaddle-gpu==3.2.0 \
  -i https://www.paddlepaddle.org.cn/packages/stable/cu126/
```

CPU-only fallback (no GPU required, noticeably slower):

```shell
python -m pip install paddlepaddle==3.2.0 \
  -i https://www.paddlepaddle.org.cn/packages/stable/cpu/
```

## 3. Install PaddleOCR with the doc-parser extra

```shell
python -m pip install "paddleocr[doc-parser]"
```

The `[doc-parser]` extra pulls in `paddlex` (the pipeline engine), `modelscope`,
`huggingface-hub`, `pypdfium2`, and the table/formula recognition stack.

## 4. Install the Word export dependency

`PP-StructureV3` saves Markdown, JSON **and Word** per document. The Word writer
requires `python-docx`, which is **not** pulled in automatically:

```shell
python -m pip install python-docx
```

Without it, the run fails with `ModuleNotFoundError: No module named 'docx'` at
the very end — after all GPU inference has already succeeded.

## 5. Verify the installation

```shell
python -c "import paddle; print('paddle OK:', paddle.__version__, '| CUDA:', paddle.device.is_compiled_with_cuda())"
python -c "import modelscope; print('modelscope OK:', modelscope.__version__)"
python -c "import paddleocr; print('paddleocr OK:', paddleocr.__version__)"
python -c "import docx; print('python-docx OK')"
```

Expected on a GPU machine:

```text
paddle OK: 3.2.0 | CUDA: True
modelscope OK: 1.40.2
paddleocr OK: 3.7.0
python-docx OK
```

`CUDA: True` is the gate for everything GPU-related. If it prints `False`, the
CPU wheel is installed — see [troubleshooting](../usage/troubleshooting.md).

A warning you will likely see on every run is harmless:
`UserWarning: No ccache found` (only relevant when compiling custom operators).

## 6. Run commands via `python -m paddleocr`

Prefer

```shell
python -m paddleocr pp_structurev3 ...
```

over the bare `paddleocr` console script. Reasons:

- The console script breaks silently if the venv was ever moved (broken shebang).
- `python -m paddleocr` always uses the interpreter of the **currently activated**
  environment, so you cannot accidentally run a second PaddleOCR installation from
  Conda `base` or the system Python.

## Verified version matrix

| Component | Version |
|---|---|
| Python | 3.12.7 |
| paddlepaddle-gpu | 3.2.0 (cu126 wheels) |
| paddleocr | 3.7.0 |
| paddlex | 3.7.2 |
| modelscope | 1.40.2 |
| python-docx | 1.2.0 |
| GPU | NVIDIA RTX 4090 (24 GB VRAM) |

Next: [choose the highest-accuracy models](../models/highest-accuracy-models.md) →
[convert a single file](../usage/single-file.md) ·
[convert a folder](../usage/batch-folder.md)
