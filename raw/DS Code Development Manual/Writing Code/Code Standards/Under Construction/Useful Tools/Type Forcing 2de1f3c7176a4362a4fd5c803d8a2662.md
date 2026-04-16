# Type Forcing

Level: Documentation, Function, Library
Last Edit: April 5, 2023 6:28 PM

## Type Forcing

[@accepts](https://pypi.org/project/accepts/) decorator to check arguments types. A TypeError is raised if the parameters are not  the correct type. Note that this goes beyond type hinting that is inbuilt in python by actually throwing an error when the types do not match.

This decorator replaces any within-function checks for parameter types.

**Good:**

```python
from accepts import accepts
@accepts(int)
def inc(value):
	return value+1

>>> inc(1) # ok

# multiple types can be enforced with
@accepts((int,float))
def inc(value):
	return value+1

>>> inc(1.5) # ok
>>> inc("string")
TypeError: inc() argument #0 is not instance of (<class 'int'>, <class 'float'>)

# can also include nonetype
@accepts((int,float,type(None)))
def inc(value):
	return value+1

>>> inc() # ok
```