# Preamble for Scripts

Level: Documentation, Library
Last Edit: April 5, 2023 6:31 PM

## Preamble for Scripts

For scripts, a preamble is suggested in order to give the developer, code reviewer, or client a better understanding of what the script is intended to do and when it was last updated.

Ascii art can also be manually created: [http://www.network-science.de/ascii/](http://www.network-science.de/ascii/)

**Bad:**

```python
import relevancy_engine as rel
import utils
import nltk
import process as pr
import corpus as cp
import numpy as np
from profiler import timeit

class User(object):
    """
			...
		"""
    def __init__(self, x):
				...
```

**Good:**

```python
#################################################################
#
#  _   _ ___ ___ ___ 
# | | | / __| __| _ \
# | |_| \__ \ _||   /
#  \___/|___/___|_|_\
#										  					 
#   Example User Class
#
#  	 - SFL Scientific, May 2021
#	   
#################################################################
import relevancy_engine as rel
import utils
import nltk
import process as pr
import corpus as cp
import numpy as np
from profiler import timeit

class User(object):
    """
			...
		"""
    def __init__(self, x):
				...
```