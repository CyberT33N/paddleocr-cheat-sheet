# Troubleshooting

Real failure signatures observed with paddleocr 3.7.x, with fixes.

## `command not found: paddleocr`

The venv is not active (prompt still shows `(base)` or your default prompt), or
you opened a fresh terminal.

```shell
cd <PROJECT_DIR>
source .venv/bin/activate
which python   # must print <PROJECT_DIR>/.venv/bin/python
```

Or bypass the entry point entirely — `python -m paddleocr ...` (recommended in
every document of this cheat sheet).

## `bad interpreter: /.../old/path/.venv/bin/python: no such file or directory`

The venv was moved or renamed after creation. Venvs are **not relocatable**. Fix:

```shell
rm -rf <PROJECT_DIR>/.venv
python3 -m venv <PROJECT_DIR>/.venv
source <PROJECT_DIR>/.venv/bin/activate
# then repeat steps 2–4 of the installation guide
```

## `ModuleNotFoundError: No module named 'modelscope'` (or `paddle`, `paddleocr`)

You are running the **wrong Python** — typically Conda `base` or a system
install that happens to have its own `paddleocr` script. The traceback gives it
away when paths point to e.g. `~/miniconda3/` instead of your venv. Fix:
activate the venv, verify `which python`, and use `python -m paddleocr`.

## `ModuleNotFoundError: No module named 'docx'`

Word export dependency missing. It fails at the **end** of a run, after
inference already succeeded:

```shell
python -m pip install python-docx
```

## `paddle.device.is_compiled_with_cuda()` returns `False`

The CPU wheel is installed. Replace it:

```shell
python -m pip uninstall -y paddlepaddle paddlepaddle-gpu
python -m pip install paddlepaddle-gpu==3.2.0 \
  -i https://www.paddlepaddle.org.cn/packages/stable/cu126/
```

## `UserWarning: No ccache found`

Harmless. Only relevant when compiling custom operators. Ignore it.

## First run hangs at "Checking connectivity to the model hosters"

PaddleX probes model hosts (HuggingFace / ModelScope) before downloading
weights. On flaky networks, skip the probe:

```shell
export PADDLE_PDX_DISABLE_MODEL_SOURCE_CHECK=True
```

## GPU out of memory or very slow

- Close other GPU consumers (browser hardware acceleration, games).
- The accuracy-first set runs comfortably on 24 GB VRAM (e.g. RTX 4090).
- For smaller GPUs, use the mobile model variants listed in the
  [models document](../models/highest-accuracy-models.md).

## `Not found any file in <dir>`

The `-i` directory contains no supported files — only `.pdf` and image
extensions are picked up. Check extensions and subfolders.
