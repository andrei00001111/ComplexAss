# 4310 — Complex Networks assignment

Temporal contact-network analysis. The work lives in [`assignment.ipynb`](assignment.ipynb), which
reads the aggregated networks from `G_data.xlsx` and `G_2.xlsx` and answers Questions 1–6.

The Python environment is managed with [uv](https://docs.astral.sh/uv/):

* [`pyproject.toml`](pyproject.toml) — the project name, required Python version and the top-level dependencies.
* `uv.lock` — the lock file pinning the exact version of every package.
* `.python-version` — the Python version uv should use (3.13).
* `.venv/` — the local virtual environment created by `uv sync` (never committed).

**Do not forget to install new libraries via uv. See example:**
```powershell
uv add numpy
```
## 1. Install uv

Linux / macOS:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Windows (PowerShell):

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Alternatives if you already have a package manager:

```bash
pipx install uv        # or: python -m pip install --user uv
brew install uv        # macOS / Linuxbrew
winget install --id=astral-sh.uv   # Windows
```

Restart your shell afterwards (or `source ~/.local/bin/env`) and verify:

```bash
uv --version
```

## 2. Sync the environment

From the repository root:

```bash
uv sync
```

`uv sync` reads `pyproject.toml` and `uv.lock`, downloads the Python version in `.python-version`
(3.13) if necessary, creates `.venv/` and installs the locked dependencies (`pandas`, `numpy`,
`matplotlib`, `openpyxl`, `ipykernel`). The result is byte-for-byte reproducible on any machine
because the versions come from `uv.lock`, not from `pyproject.toml`'s loose ranges.

Handy variants:

```bash
uv sync --frozen   # install exactly the lock file, without re-resolving (CI / reproducible runs)
uv lock            # re-resolve dependencies and update uv.lock
uv add <package>   # add a dependency, update uv.lock and .venv in one step
uv run python -c "import pandas, numpy, matplotlib, openpyxl; print('ok')"   # sanity check
```

You never have to activate `.venv` manually — `uv run <command>` executes the command inside it.

## 3. Run the notebook

**Option A — VS Code.** Open [`assignment.ipynb`](assignment.ipynb), click *Select Kernel* and
choose the interpreter at `.venv/bin/python` (`.venv\Scripts\python.exe` on Windows). `ipykernel`
is already installed by `uv sync`, so the environment shows up as a kernel. Then run the cells top
to bottom.

**Option B — JupyterLab.** JupyterLab is not a project dependency, so pull it in for the session
without editing the lock file:

```bash
uv run --with jupyterlab jupyter lab assignment.ipynb
```

If you would rather have JupyterLab always available, add it as a development dependency and commit
the updated `uv.lock`:

```bash
uv add --dev jupyterlab
uv run jupyter lab assignment.ipynb
git add pyproject.toml uv.lock && git commit -m "Add JupyterLab"
```

## 4. Commit `uv.lock` — including the first time it appears

`uv.lock` is the lock file: it records the exact version and hash of every direct and transitive
dependency. It is what makes `uv sync` reproducible, so it **must live in git** just like
`pyproject.toml`.

* When `uv sync`, `uv lock` or `uv add` creates `uv.lock` for the first time, commit it right away:

  ```bash
  git add pyproject.toml uv.lock
  git commit -m "Add uv.lock"
  ```

* Never add `uv.lock` to `.gitignore` and never edit it by hand — uv rewrites it.
* Whenever you add, remove or upgrade a dependency, commit `pyproject.toml` **and** the regenerated
  `uv.lock` together, so that everyone gets the same environment from `uv sync`.
* If a merge produces a conflicting lock, run `uv lock` to re-resolve and commit the result.
* Do not commit `.venv/`; it is machine-specific and is rebuilt by `uv sync`.
