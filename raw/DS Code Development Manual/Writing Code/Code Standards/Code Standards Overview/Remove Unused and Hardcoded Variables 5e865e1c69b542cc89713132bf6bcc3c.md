# Remove Unused and Hardcoded Variables

Level: Function, Notebook cell
Constraint: Hard
Last Edit: July 22, 2025 9:39 AM
Project Types: GA, MVP, POC

## Remove Unused Variables & Hardcoded Variables

Remove any unused variables as they can have a runtime impact and add to confusion in the purpose of the code.

Hardcoded variables should be specified in a config, an argument or in the worse case set as a global parameter. 

**Bad:** 

```python
def foo(x, y):
		z = x**2
		return 2*y

def scale(x, y):
		z = 14 * x * y
		return z
```

Note in above example, the `foo` function only uses `y`. While `x` is an input and used to define `z`, neither are returned.

**Good:**

```python
def foo(y):
		return 2*y

# make more general
def scale(x, y, sc = 14):
		z = sc * x * y
		return z

# or also fine if 14 is a global variable
SCALE_FACTOR = 14
def scale(x, y):
		z = SCALE_FACTOR * x * y
		return z
```

⭐️ Pro-tip: Some IDEs like VSCode, Pycharm, and some linter packages can check for unused variables automatically (see `possibly-unused-variable` in [Pylint features](http://pylint.pycqa.org/en/latest/technical_reference/features.html) for an example).

foo, _ = bar()