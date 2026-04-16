# TQDM for Iterators

Level: Notebook cell
Last Edit: April 5, 2023 6:28 PM

## TQDM and Iterator

To monitor progress of iterators in Jupyter notebooks for processes like model training, preferably use [tqdm.notebook](https://github.com/tqdm/tqdm#ipython-jupyter-integration) and avoid iterative outputs using `print` . This produces a cleaner final notebook without unnecessary cell outputs. 

TQDM is a flexible progress bar decorator for all iterators, and includes callbacks for tensorflow.

**Bad:**

```python
import time
import numpy as np

# nested loops with status printed every other inner loop
for i in range(4):
    print('Outer Loop {} of 4'.format(i))
    for j in range(10):
        time.sleep(0.01)
				if np.mod(j,2) == 1:
            print('    Inner Loop {} of 10'.format(j))

# cell output
Outer Loop 0 of 4
    Inner Loop 1 of 10
    Inner Loop 3 of 10
    Inner Loop 5 of 10
    Inner Loop 7 of 10
    Inner Loop 9 of 10
Outer Loop 1 of 4
    Inner Loop 1 of 10
    Inner Loop 3 of 10
    Inner Loop 5 of 10
    Inner Loop 7 of 10
    Inner Loop 9 of 10
Outer Loop 2 of 4
    Inner Loop 1 of 10
    Inner Loop 3 of 10
    Inner Loop 5 of 10
    Inner Loop 7 of 10
    Inner Loop 9 of 10
Outer Loop 3 of 4
    Inner Loop 1 of 10
    Inner Loop 3 of 10
    Inner Loop 5 of 10
    Inner Loop 7 of 10
    Inner Loop 9 of 10

```

**Good:**

![](../../Code%20Standards%20Overview/TQDM%20for%20Iterators/Animation2.gif)