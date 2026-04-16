# Benchit for Benchmarking

Level: Notebook cell
Constraint: Soft
Last Edit: April 4, 2023 3:58 PM

## Benchit for Benchmarking

In Jupyter notebooks, runtime benchmarking of functions can be done with [benchit](https://pypi.org/project/benchit/). 

Besides visualizing runtime changes with input data size, this module also outputs the details of the environment in which the function was tested — the CPU, RAM, kernel, OS and python version.
**Good:**

```python
# install package
!pip3 install benchit --user

# import
import benchit
benchit.setparams(environ='notebook')

# wrap custom functions to be tested so that the only inputs are the dataset size
def funcWrap1(N:int):
    ...
    output = testfunction(array_size=N)
    ...
		return

# run benchmarking
funcs = [funcWrap1]
inputs = {N:N for N in [3, 5, 10, 15, 25] }

t = benchit.timings(funcs, inputs, input_name='N_paint')
plotted = t.plot(logx=False, logy=False, sp_ncols=1, sp_argID=0, sp_sharey='g')
```

![](Benchit%20for%20Benchmarking/Untitled.png)