# Dependency and Environment Management

Reproducible environments across developer machines, CI, and production are a hard requirement. Getting this right prevents the "works on my machine" class of failures.

## Core Rules (Tool-Agnostic)

1. **Always pin in CI and production** — abstract deps (`pandas>=2.0`) are fine for human readability; the lockfile with exact pins is what installs in automated environments
2. **Isolate every project** — never use the system Python; always use a venv, conda environment, or Poetry-managed venv. Add `.venv/` to `.gitignore`
3. **Document the Python version** — specify it in `.python-version` (pyenv), `pyproject.toml`, and CI config. Version mismatches are a common source of confusing failures
4. **Separate runtime and dev dependencies** — test frameworks, formatters, and notebook servers must not ship in production images
5. **Review lockfile diffs in PRs** — changes to `poetry.lock` or `requirements.txt` may introduce new transitive dependencies or breaking version bumps. Treat them as code, not noise

## Options by Stack

### pip-tools / uv (Recommended for Pure Python)

Separate abstract and concrete requirements:

```
requirements.in          # direct deps with loose constraints
requirements.txt         # pinned lockfile (generated, committed)
requirements-dev.in      # dev-only deps
requirements-dev.txt     # pinned dev lockfile
```

With **uv** (faster, modern replacement for pip + pip-tools):
```bash
uv venv .venv
uv pip compile requirements.in -o requirements.txt
uv pip sync requirements.txt requirements-dev.txt
```

### Poetry (Clean Single-Tool Solution)

Best for pure-Python projects that don't need conda. Manages `pyproject.toml` (abstract) and `poetry.lock` (exact pins) together:

```bash
poetry add pandas scikit-learn
poetry add --group dev pytest ruff
poetry install --sync
```

**Always commit `poetry.lock`.** It is the mechanism that makes other developers' environments match yours.

### Conda (When You Need Binary Packages)

Use conda (or mamba for speed) when your stack includes CUDA-compiled packages, GDAL, or other packages with non-trivial C extensions that PyPI wheels don't cover reliably:

```yaml
# environment.yml
name: my-project
channels: [conda-forge, defaults]
dependencies:
  - python=3.11
  - cudatoolkit=11.8
  - pip
  - pip:
    - -r requirements.txt
```

Export two forms:
```bash
conda env export --from-history > environment.yml   # cross-platform, human-readable
conda list --explicit > conda-lock.txt              # exact OS-specific lock
```

## Data and Model Artifacts: DVC

Large files (datasets, trained models) must not live in git. Use **DVC**:

```bash
dvc add data/training_set.parquet     # creates .dvc pointer file
git add data/training_set.parquet.dvc # commit the pointer
dvc push                               # push data to S3/GCS remote
```

`dvc repro` re-executes the full pipeline from any historical commit using the exact data and code at that point.

## pyproject.toml Tool Configuration

Centralise all tool config in `pyproject.toml`:

```toml
[tool.black]
line-length = 88
target-version = ["py311"]

[tool.ruff]
line-length = 88
select = ["E", "F", "W", "I", "N", "UP"]

[tool.isort]
profile = "black"

[tool.pytest.ini_options]
testpaths = ["tests"]
```

## Source
- pip-tools: https://pip-tools.readthedocs.io/
- Poetry: https://python-poetry.org/docs/
- uv: https://docs.astral.sh/uv/
- DVC: https://dvc.org/doc/
- The Turing Way: https://the-turing-way.netlify.app/reproducible-research/renv.html

## Related
- [[workflow/project-structure]]
- [[principles/configuration-management]]
- [[cloud/cicd-for-ml]]
