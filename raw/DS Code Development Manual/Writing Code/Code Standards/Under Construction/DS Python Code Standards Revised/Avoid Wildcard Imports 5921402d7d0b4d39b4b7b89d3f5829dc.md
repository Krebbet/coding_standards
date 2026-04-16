# Avoid Wildcard Imports

Last Edit: April 5, 2023 6:26 PM
Level: Hard

## Avoid Wildcard Imports

Avoid wildcard imports will make clear where functions are coming from. 

**Bad:**

```python
from scipy import *
```

**Good:**

```python
from scipy import stats
```