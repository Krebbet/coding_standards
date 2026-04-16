# FIXME and TODOs During Development

Level: Documentation, Repo
Constraint: Soft
Last Edit: April 4, 2023 3:58 PM

In general try to use the key comments/flags `TODO` or `FIXME` when developing to ensure ease of code cleaning. All to-dos, FIXMEs and commented code should be resolved and removed before merging into the main branch. 

To preserve the development code or notes, please consider: 

1. Conditional clause to skip the code in production with a DEBUG flag. `if DEBUG: do something` , etc.
2. Add detailed inline comments so other developers can easily picked up with `TODO` or `FIXME`.
3. Open a Github issue to track a unsolved to-do or FIXME.

**Example:**

```python
def foo(x, y):
	# TODO implement function
	pass

def bar(x, y):
	return x / y # FIXME doesn't work for y = 0
```