# Return Types Must Match

Level: Function
Constraint: Hard
Last Edit: October 26, 2023 2:22 PM
Project Types: GA, MVP, POC

## Return Types Must Match

Return outputs from a function should be of the same type. This ensures all downstream code will operate correctly and not be tightly tethered to knowledge of the internals of the function.

**Bad:**

```python
def greater_5(x):
	if x > 5: 
		return True
	else: # this else line is redundant in this function
		return -1 # type mismatch

def my_function(...):
	if ... some condition...:
		return 8
	return [1, 2, 4]
```

**Good:**

```python
def greater_5(x):
	if x > 5: 
		return True
	return False # notice you can remove the "else" as you don't it!

def my_function(...):
	if ... some condition...:
		return [8]
	return [1, 2, 4]
```