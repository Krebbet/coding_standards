# Log Not Print

Last Edit: April 5, 2023 6:42 PM
Level: Variable

## Log Not Print

In scripts (i.e. not in notebooks) the default behavior should always be to log messages and not print; this allows tracking of issues for downstream analysis and is in general good accounting. 

**Bad:**

```python
# BAD
print("Hello World...")
print("Model training has completed in t="+str(x))
```

**Good:**

```python
import logging
logger = logging.getLogger(__name__)

...
logger.info("Training top model layers ...")

if __name__ == "__main__":
	logging.basicConfig(level=logging.INFO)
```