# Logger Instantiation

Level: Repo
Constraint: Hard
Last Edit: July 17, 2025 3:38 PM
Project Types: GA, MVP, POC

Each Python file should create its own logger using `logging.getLogger(__name__)` near the top, after imports. This ensures logs are tagged with the module name.

The keyword `__name__` in python returns the name of the current file. Instantiating the logger with this in the file it’s used ensures that all logs can be traced back to the file they came from. 

If a logger is passed from one file to another, then the log will erroneously name the file it was instantiated in, not the file the log came from, causing confusion!

```python
import logging

# Each file with logging should create a logger named after the file
# At the root level of the file after imports
logger = logging.getLogger(__name__)

def foo(bar):
	if bar == 0:
	
	logger.info("")

if __name__ == "__main__":
  # Define the config inside a `__name__ == "__main__":`
  # This ensures it's only set for this entrypoint.
  # Meaning others can import foo while using their own config settings.
	logging.basicConfig()
```