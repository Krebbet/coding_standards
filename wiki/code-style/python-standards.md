# Python Code Standards

Style and formatting rules for all Python code. These are enforced via automated tooling where possible.

## PEP 8 is the Default

When no other standard on this page applies, follow [PEP 8](https://peps.python.org/pep-0008/). GitHub Actions enforce PEP 8 formatting via **Black**.

**Constraint level:** Hard — applies to all project types (PoC, MVP, GA).

## Formatting: Black

All code must be formatted with `black` before committing. Do not spend code review time on style — let the formatter decide.

## Naming Conventions (PEP 8)

| Item | Convention | Example |
|---|---|---|
| Variables | `snake_case` | `total_price` |
| Functions | `snake_case` | `calculate_total()` |
| Classes | `PascalCase` | `OrderProcessor` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Private members | `_leading_underscore` | `_internal_state` |
| Module names | `snake_case` | `data_pipeline.py` |

## Strings: Double Quotes

Use double-quoted strings consistently: `"Hello"` not `'Hello'`. Black enforces this.

## Imports

- All imports at the top of the file
- No wildcard imports (`from module import *`) — they pollute the namespace and make it impossible to trace where names come from
- Import only what you use
- Order: standard library → third-party → local, separated by blank lines (enforced by `isort`)

```python
# Good
import os
from pathlib import Path

import pandas as pd
import numpy as np

from mypackage import utils
```

## Path Handling

Use `pathlib.Path` (preferred) or `os.path.join()` for constructing paths. Never use string concatenation or f-strings for file paths.

```python
# Good
from pathlib import Path
data_dir = Path("data") / "raw" / "input.csv"

# Also acceptable (legacy)
import os
path = os.path.join("data", "raw", "input.csv")

# Bad
path = "data" + "/" + "raw" + "/" + "input.csv"
```

## String Combinations

Use f-strings for readability. Avoid `+` concatenation for anything beyond trivial joins.

```python
# Good
message = f"Training completed in {elapsed:.2f}s"

# Bad
message = "Training completed in " + str(elapsed) + "s"
```

## Mutable Default Arguments

Never use mutable objects as default argument values. Python creates the default object once, and mutations persist across calls.

```python
# Bad
def process(items=[]):
    items.append(1)
    return items

# Good
def process(items=None):
    if items is None:
        items = []
    items.append(1)
    return items
```

## Return Types Must Match

A function's return type must be consistent. Do not return `str` on success and `None` on failure, or mix types based on conditions. Use `Optional[T]` explicitly if `None` is a valid return.

## Remove Unused Code Before Merging

Before merging to master, remove:
- Commented-out code
- Unused variables
- Hardcoded/magic values (extract to named constants or configuration)
- TODO/FIXME comments — track these in GitHub Issues instead

## Requirements Files: Pin Versions

All dependencies in `requirements.txt` must include specific version pins:

```
pandas==2.1.0
numpy==1.26.0
```

This ensures reproducibility across environments.

## Avoid Unnecessary Conditionals

Prefer positive conditions and early returns. Deeply nested if/else blocks are a code smell — flatten or extract.

```python
# Bad — deeply nested
def process(data):
    if data:
        if data["status"] == "active":
            if data["value"] > 0:
                return data["value"] * 2

# Good — guard clauses
def process(data):
    if not data:
        return None
    if data["status"] != "active":
        return None
    if data["value"] <= 0:
        return None
    return data["value"] * 2
```

## TQDM for Progress on Iterators

Use `tqdm` to show progress on long-running iterations. It integrates with logging-friendly output and adds negligible overhead.

## Try/Except: Be Specific

Catch specific exception types. Never use a bare `except:` or `except Exception:` unless you re-raise or log the full traceback. Swallowed exceptions are one of the hardest bugs to debug.

## Source
- `../raw/DS Code Development Manual/Writing Code/Code Standards/Code Standards Overview/` (multiple pages)
- `../raw/DS Code Development Manual/Writing Code/DRAFT Updated Code Practices…md`

## Related
- [[code-style/docstrings-and-types]]
- [[code-style/code-structure]]
- [[principles/logging]]
