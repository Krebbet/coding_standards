# Python Style Standards for Data Science Work

## Source

- https://peps.python.org/pep-0008/
- https://google.github.io/styleguide/pyguide.html
- https://black.readthedocs.io/en/stable/
- https://pycqa.github.io/isort/
- https://mypy.readthedocs.io/en/stable/
- https://docs.astral.sh/ruff/

---

## Baseline: PEP 8 + Automated Formatting

PEP 8 is the foundational style guide for Python. Rather than debating line length or quote style, data science teams should delegate those decisions to **Black** (opinionated formatter) and **isort** (import sorter), enforced via pre-commit hooks. This eliminates style arguments in code review and keeps diffs focused on logic.

Recommended toolchain in `pyproject.toml`:

```toml
[tool.black]
line-length = 88
target-version = ["py311"]

[tool.isort]
profile = "black"   # ensures isort and Black agree on import formatting

[tool.ruff]
line-length = 88
select = ["E", "F", "W", "I", "N", "UP"]  # pycodestyle, pyflakes, isort, naming, upgrades
```

Use `ruff` as a fast combined linter/formatter replacement for flake8 + isort in new projects; Black remains the formatting standard for code style uniformity.

## Data Science-Specific Style Additions

### Imports

Follow the standard three-block import convention, separated by blank lines:

1. Standard library (`os`, `pathlib`, `typing`)
2. Third-party (`numpy`, `pandas`, `sklearn`)
3. Local package (`from src.features import build_features`)

Avoid star imports (`from numpy import *`) entirely. They pollute the namespace and make static analysis impossible.

### Type Annotations

Use type hints for all function signatures in `src/`. Data science code often skips this, but annotations on transform functions dramatically improve IDE support and catch shape/dtype bugs early.

```python
import numpy as np
import pandas as pd

def normalise(df: pd.DataFrame, columns: list[str]) -> pd.DataFrame:
    """Return df with specified columns scaled to [0, 1]."""
    result = df.copy()
    result[columns] = (df[columns] - df[columns].min()) / (df[columns].max() - df[columns].min())
    return result
```

Use `np.ndarray` for arrays with a shape comment where the shape is non-obvious: `# shape: (n_samples, n_features)`.

### Naming Conventions

| Object | Convention | Example |
|---|---|---|
| Variables and functions | `snake_case` | `train_model`, `feature_matrix` |
| Classes | `PascalCase` | `FeaturePipeline` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_EPOCHS`, `RANDOM_SEED` |
| DataFrame columns | `snake_case` strings | `customer_id`, `revenue_usd` |
| Private helpers | Leading underscore | `_validate_schema` |

Avoid single-letter variable names except as conventional loop indices (`i`, `j`) or in mathematical formulas where the name mirrors the notation in a referenced paper (document this in a docstring).

### Docstrings

Follow Google style docstrings for functions and classes. NumPy docstring style is also acceptable and is preferred when documenting array shapes and dtypes extensively.

```python
def compute_roc_auc(y_true: np.ndarray, y_score: np.ndarray) -> float:
    """Compute the ROC-AUC score for a binary classifier.

    Args:
        y_true: Ground-truth binary labels, shape (n_samples,).
        y_score: Predicted probability of the positive class, shape (n_samples,).

    Returns:
        ROC-AUC score as a float in [0, 1].

    Raises:
        ValueError: If y_true contains more than two distinct values.
    """
```

### Magic Numbers and Seeds

Always name numeric constants and fix random seeds explicitly at the entry point of a pipeline:

```python
RANDOM_SEED = 42
TEST_SIZE = 0.2

rng = np.random.default_rng(RANDOM_SEED)
```

This makes experiments reproducible and makes the meaning of each constant searchable.
