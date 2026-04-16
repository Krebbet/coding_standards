# Headers with Comments to Separate Code Sections

Last Edit: April 5, 2023 6:46 PM
Level: Medium

## Headers with Comments to Separate Code Sections

Add block comment as a header for each section. This will help you and others reviewing your code more easily navigate the script. The default proposed separation is:

```python
#-------------#
# LIBRARIES 
#-------------#
```

Can also use smaller comments for subsections. In some cases these subsections are implied and do not need to be explicitly stated.

```python
#
# Classes 
#
```

**Good:**

```python
#---------------#
# LIBRARIES 
#---------------#
# General #
import os
import sys

# Data Science #
import numpy as np
import pandas as pd 

#---------------#
# DRONES 
#---------------#
# Classes #
class Drone:
	def __init__(self, name="Snail", max_speed=9001):
    self.name = name
		self.max_speed = max_speed

class Navigator:
	def __init__(self, name):
    self.name = name
    self.expert = True

#
# Functions 
#
...
```