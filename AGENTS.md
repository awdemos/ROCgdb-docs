# ROCgdb-docs

Documentation repository for AMD ROCgdb — the source-level debugger for ROCm. The published docs live at https://rocm.docs.amd.com/projects/ROCgdb/en/latest/.

## Project layout

```
docs/               Sphinx documentation source
  conf.py           Sphinx configuration; pulls version from ROCgdb/gdb/version.in
  index.rst         Root toctree
  how-to/           How-to guides
  install/          Installation instructions
  quick-reference/  Quick reference cards
  sphinx/           ROCm Docs theming and table-of-contents
  _static/          Static assets (including generated PDFs)
  data/             Image assets
ROCgdb/             Git submodule containing the upstream ROCgdb source
_readthedocs/       Read the Docs build output directory
build_docs.sh       Shell script to build ROCgdb HTML/PDF and stage for RTD
```

## Setup commands

```bash
# Clone with the ROCgdb submodule
git clone --recursive https://github.com/awdemos/ROCgdb-docs.git
# or, if already cloned:
git submodule update --init --recursive

# Install Sphinx deps
python3 -m pip install -r docs/sphinx/requirements.txt
```

## Build/test/lint commands

```bash
# Build only the ROCgdb HTML/PDF docs (stages output in _readthedocs/html)
./build_docs.sh

# Build the ROCgdb-docs Sphinx site locally
cd docs
python3 -m sphinx -T -E -b html -d _build/doctrees -D language=en . _build/html

# Build and merge GDB HTML into the Sphinx site
cd ..
git submodule update --init --recursive
cd ROCgdb
./configure
make
make do-html
cd ..
cp -v --parents $(find ROCgdb/ -name "*.html") docs/_build/html
```

## Key conventions

- Documentation uses Sphinx with the ROCm Docs theme (`rocm_docs`).
- Version is extracted from `ROCgdb/gdb/version.in` at build time.
- The `ROCgdb` submodule is GPL-licensed; derived docs carry ROCgdb's license, so `docs/conf.py` copies `ROCgdb/COPYING` to `LICENSE`.
- ReadTheDocs config is in `.readthedocs.yaml`; formats include `htmlzip`, `pdf`, and `epub`.
- Contributing guidelines are in `CONTRIBUTING.md` and follow the broader [ROCm contribution guide](https://rocm.docs.amd.com/en/latest/contribute/contributing.html).

## Gotchas

- `build_docs.sh` removes and recreates `_build` and `_readthedocs/html`; do not point it at uncommitted work inside `_build`.
- The submodule must be initialized before running `build_docs.sh`; the script will fail with missing source files otherwise.
- `docs/conf.py` runs `git submodule update --init` automatically; local offline builds may need the submodule already present.
- Generated PDFs are committed under `docs/_static/pdf/ROCgdb/` after a build; keep them in sync when the ROCgdb submodule is updated.
