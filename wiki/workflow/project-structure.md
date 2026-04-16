# Project Structure and Repository Layout

A consistent directory layout reduces onboarding time, makes CI/CD tractable, and signals to collaborators where things live without needing to ask. This layout is derived from Cookiecutter Data Science (v2) and The Turing Way.

## Recommended Layout

```
project-root/
├── data/
│   ├── raw/          # Immutable original data — never modify
│   ├── interim/      # Intermediate transformed data
│   ├── processed/    # Final datasets used for modelling
│   └── external/     # Data from third-party sources
├── docs/             # Project documentation
├── models/           # Serialised trained models and metadata
├── notebooks/        # Jupyter notebooks (numbered: 01-eda.ipynb)
├── reports/
│   └── figures/      # Generated charts and figures
├── src/
│   └── <package>/    # Importable Python source
│       ├── __init__.py
│       ├── data/     # Data loading / download scripts
│       ├── features/ # Feature engineering transforms
│       ├── models/   # Training and prediction logic
│       └── viz/      # Visualisation helpers
├── tests/            # Unit and integration tests (mirrors src/)
├── .env.example      # Config template — never commit .env
├── pyproject.toml    # Project metadata, deps, tool config
├── Makefile          # Reproducibility entry points
└── README.md
```

## Key Principles

### Raw Data is Sacred
`data/raw/` is read-only. All transforms produce outputs into `data/interim/` or `data/processed/`. This makes the pipeline re-runnable from the original source with no ambiguity about what was changed.

### Notebooks Are Not Source Code
Exploratory notebooks live in `notebooks/` and must not be imported by `src/`. Use numbered naming (`01-eda-sales.ipynb`, `02-feature-engineering.ipynb`) to communicate sequence. Once logic is stable, refactor it into `src/`. See [[workflow/notebooks-vs-scripts]].

### Make the Package Installable
Include a `pyproject.toml` that makes `src/<package>` installable via `pip install -e .`. This eliminates fragile `sys.path.append` hacks in notebooks and lets `tests/` import source code cleanly.

### Makefile as Executable Documentation
A `Makefile` with targets like `make data`, `make features`, `make train`, and `make test` documents the full pipeline as runnable commands. New contributors can reproduce results by reading five lines rather than five paragraphs.

```makefile
.PHONY: data features train test

data:
    python src/data/download.py

features:
    python src/features/build_features.py

train:
    python src/models/train_model.py

test:
    pytest tests/
```

### Separate Configuration from Code
Store environment-specific settings in `.env` files loaded at runtime. Commit `.env.example` with placeholders; never commit `.env`. Model hyperparameters belong in versioned YAML/TOML config files, not hard-coded in notebooks. See [[principles/configuration-management]].

## Version Control Hygiene

Add to `.gitignore`:
```
data/raw/
data/processed/
models/
.env
.venv/
__pycache__/
*.pyc
```

For large files (datasets, model artifacts), use DVC to track them without bloating git history. See [[workflow/dependency-management]].

## Source
- Cookiecutter Data Science v2: https://cookiecutter-data-science.drivendata.org/
- The Turing Way: https://the-turing-way.netlify.app/

## Related
- [[workflow/notebooks-vs-scripts]]
- [[workflow/dependency-management]]
- [[principles/configuration-management]]
- [[principles/data-pipeline-design]]
