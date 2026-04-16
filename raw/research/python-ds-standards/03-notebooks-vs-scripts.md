# Notebook vs Script Guidelines: When to Use Each

## Source

- https://the-turing-way.netlify.app/reproducible-research/code-quality.html
- https://cookiecutter-data-science.drivendata.org/opinions/#notebooks-are-for-exploration-and-communication
- https://nbconvert.readthedocs.io/en/latest/
- https://nbqa.readthedocs.io/en/latest/
- https://papermill.readthedocs.io/en/latest/

---

## The Core Distinction

Jupyter notebooks and Python scripts serve different purposes. Conflating them produces code that is neither good for exploration nor good for production. The rule of thumb from Cookiecutter Data Science is blunt: **notebooks are for exploration and communication; scripts are for transformation and production.**

## When to Use Notebooks

Notebooks are the right tool when:

- **Exploring a dataset for the first time** — inline plots, `.head()` calls, and `.describe()` output belong in a notebook where you can see them alongside the code that produces them.
- **Communicating findings to non-engineers** — a notebook rendered via `nbconvert` to HTML or PDF is a natural format for a stakeholder report that mixes narrative, code, and charts.
- **Prototyping a new model or feature** — the interactive feedback loop of cell-by-cell execution is genuinely valuable when you do not yet know what shape the solution will take.
- **Teaching or documentation** — step-by-step walkthroughs are clearer as notebooks than as scripts with extensive comments.

### Notebook Best Practices

- **Number notebooks sequentially**: `01-eda.ipynb`, `02-feature-engineering.ipynb`. This communicates the intended execution order.
- **Clear outputs before committing**: Committed output cells bloat diffs and expose data. Use `nbstripout` as a pre-commit hook, or configure it globally.
- **Keep notebooks linear**: A notebook that requires cells to be run out of order is a bug. Use "Restart and Run All" as a smoke test before committing.
- **Apply linting with nbQA**: `nbqa ruff notebooks/` applies ruff to notebook cells, catching obvious errors without disrupting the notebook workflow.
- **Limit notebook length**: If a notebook exceeds ~500 lines of code cells, it is doing too much. Split by purpose.

## When to Use Scripts and Modules

Migrate logic from notebooks to `src/` Python modules when:

- **The code will run unattended** — scheduled jobs, CI pipelines, and batch inference should be scripts, not notebooks. Notebooks have no reliable mechanism for surfacing errors to orchestrators.
- **The code is reused across notebooks** — a function used in three notebooks belongs in `src/`, not copy-pasted.
- **The logic needs testing** — `pytest` works on modules, not notebook cells. Anything load-bearing must be unit-tested, which requires it to be in a module.
- **Performance is a concern** — the per-cell state management of notebooks adds overhead and makes profiling difficult.

### The Refactoring Pattern

The recommended workflow is:

1. Explore in a notebook until the approach is clear.
2. Extract stable, reusable logic into `src/<package>/` functions.
3. Import those functions back into the notebook for use — the notebook now documents the workflow while the logic lives where it can be tested.
4. Write a script or pipeline stage that calls `src/` functions directly for production use.

## Parameterised Notebooks as a Middle Ground

For cases where a notebook is the intended artifact (e.g., a weekly automated report), use **Papermill** to parameterise and execute notebooks from the command line:

```python
# In the notebook, tag a cell as "parameters":
run_date = "2026-01-01"
region = "EU"
```

```bash
papermill template_report.ipynb output/report_2026-04-15.ipynb \
  -p run_date 2026-04-15 -p region US
```

This preserves the notebook format for the output while making the run reproducible and scriptable. It is a good pattern for reporting pipelines but should not be used as a substitute for proper pipeline code in production ML systems.
