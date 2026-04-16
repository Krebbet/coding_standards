# Notebooks vs Scripts: When to Use Each

Jupyter notebooks and Python scripts serve different purposes. The rule: **notebooks are for exploration and communication; scripts and modules are for transformation and production.**

## When to Use Notebooks

Notebooks are the right tool for:
- **Exploring a dataset for the first time** — inline plots, `.head()`, `.describe()` belong alongside the code that produces them
- **Communicating findings to non-engineers** — notebooks rendered via `nbconvert` are a natural format for stakeholder reports mixing narrative, code, and charts
- **Prototyping** — the interactive feedback loop of cell-by-cell execution is genuinely useful when you don't yet know what shape the solution will take
- **Teaching and documentation** — step-by-step walkthroughs are clearer as notebooks

### Notebook Best Practices
- **Number sequentially**: `01-eda.ipynb`, `02-feature-engineering.ipynb`
- **Strip outputs before committing**: use `nbstripout` as a pre-commit hook — committed outputs bloat diffs and may expose data
- **Keep notebooks linear**: a notebook requiring out-of-order execution is a bug. Use "Restart and Run All" before committing as a smoke test
- **Lint with nbQA**: `nbqa ruff notebooks/` catches errors without breaking the notebook workflow
- **Limit length**: if a notebook exceeds ~500 lines of code cells, split it by purpose

## When to Use Scripts and Modules

Migrate logic from notebooks to `src/` Python modules when:
- **The code runs unattended** — scheduled jobs, CI pipelines, and batch inference should be scripts. Notebooks have no reliable error surfacing for orchestrators
- **The code is reused across notebooks** — a function used in three notebooks belongs in `src/`, not copy-pasted
- **The logic needs testing** — `pytest` works on modules, not notebook cells. Anything load-bearing must be unit-tested
- **Performance matters** — per-cell state management adds overhead and makes profiling difficult

## The Refactoring Pattern

The recommended workflow:

1. Explore in a notebook until the approach is clear
2. Extract stable, reusable logic into `src/<package>/` functions
3. Import those functions back into the notebook — the notebook now documents the workflow while the logic lives where it can be tested
4. Write a script or pipeline stage that calls `src/` functions directly for production use

```python
# In notebook: import from src, don't redefine
from src.features.transforms import normalise, bucketise_age

df_clean = normalise(df_raw, columns=["revenue", "spend"])
```

This keeps the notebook thin and the logic testable.

## Parameterised Notebooks as a Middle Ground

For cases where a notebook *is* the intended artifact (e.g., a weekly automated report), use **Papermill** to parameterise and execute notebooks from the command line:

```bash
papermill template_report.ipynb output/report_2026-04-16.ipynb \
  -p run_date 2026-04-16 -p region EU
```

This is appropriate for reporting pipelines but should not substitute for proper pipeline code in production ML systems.

## Source
- Cookiecutter Data Science: https://cookiecutter-data-science.drivendata.org/opinions/
- The Turing Way: https://the-turing-way.netlify.app/reproducible-research/code-quality.html

## Related
- [[workflow/project-structure]]
- [[testing/writing-good-tests]]
- [[principles/data-pipeline-design]]
