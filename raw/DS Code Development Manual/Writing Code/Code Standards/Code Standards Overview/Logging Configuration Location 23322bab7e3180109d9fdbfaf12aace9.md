# Logging Configuration Location

Level: Repo
Constraint: Hard
Last Edit: July 17, 2025 3:40 PM
Project Types: GA, MVP, POC

Logging configuration (like `logging.basicConfig()`) should only be set inside the `if __name__ == "__main__":` block. This way, the config is applied only when the file is run directly—not when it's imported—allowing other scripts to apply their own logging setup without interference.

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

## 🔴 **Bad Practice Example: `bad_module.py`**

```python
# bad_module.py
import logging

# ❌ BAD: Configuring logging at import time
logging.basicConfig(level=logging.DEBUG)

logger = logging.getLogger(__name__)

def foo():
    logger.debug("This is a debug message from bad_module.")
```

### **Main Script: `main.py`**

```python
# main.py
import logging
import bad_module
if __name__ == "__main__":
	# ✅ GOOD: Main script sets its own logging configuration
	logging.basicConfig(level=logging.WARNING)
	bad_module.foo()
```

### 🔎 What Happens?

When `main.py` is run:

- `bad_module` is imported **before** `main.py`'s `basicConfig()` is called.
- But in Python, **`basicConfig()` only has an effect the first time it's called**.
- Since `bad_module.py` calls `basicConfig()` on import, it sets the logging config globally—before `main.py` gets a chance.

As a result:

- `bad_module` sets the logging level to `DEBUG`.
- Even though `main.py` *tries* to override it with `WARNING`, it **has no effect**.
- You will see **debug messages** from `bad_module`, even though `main.py` wanted to suppress them.

### 🧾 Output

```
DEBUG:bad_module:This is a debug message from bad_module.
```

## ✅ **Correct Practice Example: `good_module.py`**

```python
# good_module.py
import logging

# ✅ Good: No logging config at import time
logger = logging.getLogger(__name__)

def foo():
    logger.debug("This is a debug message from good_module.")
```

### **Main Script: `main.py`**

```python
# main.py
import logging
import bad_module
if __name__ == "__main__":
	# ✅ GOOD: Main script sets its own logging configuration
	logging.basicConfig(level=logging.WARNING)
	bad_module.foo()
```

### 🔎 What Happens?

When `main.py` is executed:

- `good_module` is imported and defines its logger but **does not configure** logging.
- `main.py` sets the logging level to `WARNING` using `basicConfig()`.
- The global logging configuration is respected by **all modules**, including `good_module`.

Since `foo()` logs at `DEBUG` level and the configured level is `WARNING`, the debug message is **not shown**, as expected.