# Avoid Wildcard Imports

Level: Library
Constraint: Hard
Last Edit: July 22, 2025 9:37 AM
Project Types: GA, MVP, POC

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