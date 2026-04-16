# Library Imports are Used & Placed at Top

Last Edit: April 5, 2023 6:28 PM
Level: Hard

## Library Imports are Used & Placed at the Top

All library imports should be placed at the top of a module. From this list, check that all library imports are utilized within the module and if not they should be deleted. 

Also check that all functions are either used within the same module and are not imported elsewhere in the same repo.

**Bad:**

```python
import pandas as pd 
import numpy as np

def foo(x,y):
		z = np.linspace(0,x,y)
		return z

import random
def bar(x):
		return random.random(x)
```

**Good:**

Note that:

- *pandas* is not utilized in the `foo.py` module and thus the first import line should be removed,
- *import* *random* should be moved to the top of the script.

```python

import numpy as np
import random

def foo(x,y):
		z = np.linspace(0,x,y)
		return z

def bar(x):
		return random.random(x)
```

In general the order of the libraries should be in order that they appear in the script, though can be grouped for further clarity for similar concepts and features. 

⭐️ Pro-tip: Some IDEs like VSCode, Pycharm, and some linter packages can check for unused imports automatically (see `unused-import` in [Pylint features](http://pylint.pycqa.org/en/latest/technical_reference/features.html) for an example).