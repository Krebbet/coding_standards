# Class Attributes Defined in Constructor

Last Edit: April 5, 2023 6:43 PM
Level: Hard

## Class Attributes Should Be Defined in the Constructor

It is important to check that all class attributes are defined in the constructor when creating a class/function, this simplifies the understanding of the code. 

Further class attributes that are set by arguments in the constructor should in general have the same name e.g. `self.x = x` and not `self.x_var = x`.

**Bad:**

```python

class foo:
	"""
		...
	"""
	def __init__(self, x):
		self.x = x
	
	def bar(self, y):
		"""
		...
		"""
		self.y = y
```

**Good:**

```python
class foo:
	"""
		...
	"""
	def __init__(self, x):
		self.x = x
		self.y = None # Added this line
	
	def bar(self, y):
		"""
		...
		"""
		self.y = y
```