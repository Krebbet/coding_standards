# Code Structure

How to organise files, functions, and classes within a Python project.

## File and Function Length

Keep units focused and short. If a function cannot be understood quickly, it is too complex.

- Functions with heavy branching logic (nested `if/elif`, loops) signal a need to decompose
- A function that does the reading, processing, and writing of data should be split into three
- Classes should have one focused purpose — avoid "god classes"

## Headers to Separate Code Sections

Use comment headers to divide logically distinct sections within a module:

```python
# ─────────────────────────────────────────
# Data Loading
# ─────────────────────────────────────────

def load_raw_data(...):
    ...

# ─────────────────────────────────────────
# Feature Engineering
# ─────────────────────────────────────────

def compute_rolling_features(...):
    ...
```

## Class Attributes in Constructor

All class attributes must be defined in `__init__`. Do not add attributes dynamically in other methods — this makes the class's state opaque.

```python
# Good
class ModelTrainer:
    def __init__(self, model, lr):
        self.model = model
        self.lr = lr
        self.history = []   # defined here, even if populated later

# Bad
class ModelTrainer:
    def __init__(self, model, lr):
        self.model = model

    def train(self, data):
        self.history = []   # attribute appears here — hidden from readers
```

## Avoid Wildcard Imports

```python
# Bad
from numpy import *

# Good
import numpy as np
from numpy import array, zeros
```

## Pandas Efficiency

Prefer vectorised operations over loops for Pandas transformations:

| Approach | Speed | Use |
|---|---|---|
| Vectorised (`df["col"] * 2`) | Fastest | Default |
| `apply()` with lambda | Moderate | When vectorisation isn't possible |
| `iterrows()` | Slowest | Avoid — only as last resort |

## Directory Structure (for tested projects)

Mirror the `src` directory structure in the `test` directory:

```
src/
  model/
    training.py
test/
  model/
    test_training.py
    fixtures/
      sample_data.csv
```

Code reviewers run tests as black-box checks. Test files must include documentation of all edge cases covered.

## Notebook Defaults

Jupyter notebooks should set display defaults at the top:

```python
import pandas as pd
pd.set_option("display.max_columns", 100)
pd.set_option("display.max_rows", 100)
pd.set_option("display.float_format", "{:.4f}".format)
```

## Source
- `../raw/DS Code Development Manual/Writing Code/Code Standards/Code Standards Overview/` (multiple pages)
- `../raw/DS Code Development Manual/Writing Code/Jupyter Notebook Guidelines…md`

## Related
- [[code-style/python-standards]]
- [[code-style/docstrings-and-types]]
- [[testing/writing-good-tests]]
