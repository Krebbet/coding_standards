# Library Imports Format & Organization

Level: Library, Notebook
Constraint: Hard
Last Edit: November 25, 2025 12:23 PM
Project Types: GA, MVP, POC

## Formatting/Organizing library imports

Check that all library imports are at the top of the module (instead of within the code), and that they are formatted and organized correctly.

**Correct formats (see [here](https://google.github.io/styleguide/pyguide.html?showone=Imports_formatting#Imports_formatting)):**

- Each import should be on a separate line
- Use `import x` for importing packages and modules.
- Use `from x import y` where `x` is the package prefix and `y` is the module name with no prefix.
- Use `from x import y as z` if two modules named `y` are to be imported or if `y` is an inconveniently long name.
- Use `import y as z` only when `z` is a standard abbreviation (e.g., `np` for `numpy`).
- For standard library and related third party imports:
    - if importing a single function from a module, use format `from module.submodule import function`
    - if using more than one function from the same (sub)module, group them during import (if 3rd party library)
- For local application/library specific imports, import the whole module without unpacking; during use, call the function and module together (`import foo` > when using bar function from the foo module, call `foo.bar()`). This helps underscore the purpose of the function (assuming the module name is named in an informative way — e.g., `util_funcs.py`, `feature_funcs.py`, `model_funcs.py`)

**Correct Organization:**

- From PEP8, the high level organization should be
    1. standard library imports (e.g., `sys`, `os`)
    2. related third party imports (e.g., `pandas`, `numpy`)
    3. local application/library specific imports (e.g., `foo`, `bar`).
- If any of the above high level organization sections have many lines (~5-10), they can be broken down further and grouped by function — please add a comment on how they are grouped (e.g., modeling vs metrics).

**Bad:** 

```python
import matplotlib.pyplot as plt
import pandas as ps
import numpy as np
import seaborn as sb
import os, sys
import sklearn.discriminant_analysis.LinearDiscriminantAnalysis
import sklearn.neighbors.KNeighborsClassifier
from sklearn.metrics import roc_curve
from sklearn.metrics import auc
from sklearn.metrics import confusion_matrix
from util_funcs import my_util_func1, my_util_func2

def foo(x,y):
		import foo_funcs
		##<code>##
```

**Good**:

```python
import os
import sys

# general functions
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns

# modeling functions
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
from sklearn.naive_bayes import GaussianNB
from sklearn.neighbors import KNeighborsClassifier

# metrics functions
from sklearn.metrics import (
    auc,
		confusion_matrix,
    roc_curve
)

# repo functions
import util_funcs
import foo_funcs

def foo(x,y):
		##<code>##
```