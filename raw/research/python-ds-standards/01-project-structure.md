# Project Structure and Repository Layout for Data Science Projects

## Source

- https://cookiecutter-data-science.drivendata.org/
- https://the-turing-way.netlify.app/project-design/project-repo.html
- https://github.com/drivendataorg/cookiecutter-data-science
- https://the-turing-way.netlify.app/reproducible-research/reproducible-research.html

---

## Why Structure Matters

Ad-hoc data science repositories accumulate notebooks with names like `analysis_final_v3_REAL.ipynb`, data files committed directly to git, and no clear separation between raw inputs and derived outputs. A consistent layout reduces onboarding time, makes CI/CD integration tractable, and signals to collaborators where things live without asking.

## Recommended Directory Layout

A layout derived from Cookiecutter Data Science (v2) and The Turing Way:

```
project-root/
├── data/
│   ├── raw/          # Immutable original data — never modify
│   ├── interim/      # Intermediate transformed data
│   ├── processed/    # Final data sets used for modelling
│   └── external/     # Data from third-party sources
├── docs/             # Project documentation (Sphinx, MkDocs, etc.)
├── models/           # Serialised trained models and model metadata
├── notebooks/        # Jupyter notebooks (numbered for ordering)
├── reports/
│   └── figures/      # Generated graphics and figures
├── src/
│   └── <package>/    # Importable Python source code
│       ├── __init__.py
│       ├── data/     # Scripts to download or generate data
│       ├── features/ # Feature engineering transforms
│       ├── models/   # Training and prediction logic
│       └── viz/      # Visualisation helpers
├── tests/            # Unit and integration tests (mirrors src/ layout)
├── .env.example      # Template for secrets — never commit .env
├── pyproject.toml    # Project metadata, deps, and tool config
├── README.md
└── Makefile          # Reproducibility entry points (make data, make train)
```

## Key Principles

**Raw data is sacred.** The `data/raw/` directory should be treated as read-only. All transformations produce outputs into `data/interim/` or `data/processed/`. This makes the pipeline re-runnable from the original source without ambiguity about what was modified.

**Notebooks are not source code.** Exploratory notebooks live in `notebooks/` and should not be imported by anything in `src/`. Use a naming convention like `01-eda-sales.ipynb`, `02-feature-engineering.ipynb` to communicate sequence and intent. Once logic is stable, refactor it into `src/`.

**Make the project installable.** Include a `pyproject.toml` that makes `src/<package>` installable via `pip install -e .`. This eliminates fragile `sys.path.append` hacks in notebooks and lets `tests/` import source code cleanly.

**Use a Makefile or task runner.** A `Makefile` with targets like `make data`, `make features`, `make train`, and `make test` documents the full pipeline in executable form. New contributors can reproduce results by reading five lines rather than five pages of documentation.

**Separate configuration from code.** Store environment-specific settings (database URLs, API keys, file paths) in `.env` files loaded via `python-dotenv`. Commit `.env.example` with placeholder values; never commit `.env` itself. Parameter settings for model runs belong in versioned config files (YAML/TOML), not hard-coded in notebooks.

## Version Control Hygiene

- Add `data/raw/`, `models/`, and `.env` to `.gitignore` unless files are small and non-sensitive.
- For large files use DVC (Data Version Control) or similar tools to track data artifacts without bloating the git history.
- The Turing Way recommends including a `CITATION.cff` file and a `LICENSE` from the start, not as an afterthought.
