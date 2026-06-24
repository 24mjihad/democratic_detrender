# AGENTS.md

## Cursor Cloud specific instructions

`democratic_detrender` is a single pip-installable Python library (no services, DBs, or
web apps) for ensemble-based detrending of stellar light curves. The "app" is the
package itself, exercised via scripts/notebooks. Standard install/test commands live in
`README.md`, `pyproject.toml`, and `.github/workflows/tests.yml`.

The startup update script creates a virtualenv at `.venv/` and installs the package with
the `[dev]` extras. Activate it before doing anything:

```bash
source .venv/bin/activate
```

### Non-obvious caveats

- **setuptools must be `<81`.** `celerite2.pymc` imports the removed `pkg_resources`
  module, so importing `democratic_detrender` fails with newer setuptools. The update
  script pins `setuptools<81` after installing; do not upgrade it past 81.
- **Figure generation requires LaTeX.** Importing the package runs
  `democratic_detrender/plot.py`, which sets `matplotlib.rc("text", usetex=True)`
  globally. Without a system TeX install, any `matplotlib` figure (even unrelated plots)
  fails with "latex could not be found". Either install a TeX toolchain
  (e.g. `texlive-latex-base dvipng cm-super`) or override
  `matplotlib.rcParams["text.usetex"] = False` after importing the package.
- **Tests:** run `pytest tests/test_*.py` (as CI does). The `testpaths` in
  `pyproject.toml` point at `test`/`docs` dirs that are not the actual `tests/` dir, so
  pass the path explicitly. The suite is fast and fully offline (heavy/external helpers
  are monkeypatched).
- **Real light-curve detrending needs network access.** `get_lc.py` fetches TESS/Kepler
  data from MAST via `lightkurve`. Offline work should use synthetic arrays fed directly
  to the algorithm functions (`cofi_AM`, `poly_AM`, `gp`, etc.).
- There is no linter configured; the closest "build" check is `python -m build`.
