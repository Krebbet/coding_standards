# Docstring Format, Type Hinting and Default Arguments

Last Edit: April 5, 2023 6:26 PM
Level: Hard

All methods and functions should have full documentation based on [Google’s style guide](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings). 

The type of each argument should be specified in the docstring, along with return values and any exceptions raised (if any). In the case that the function does not raise any exceptions explicitly the “Raises” section can be excluded.

It is important to use triple double quotes to start and end the docstring.

There are various plugins that can automatically template and even fill out these docstrings such as [AI Python Docstring Generator Plugin for VSCode](https://marketplace.visualstudio.com/items?itemName=graykode.ai-docstring).

### Sample usage for the above plugin.

[ai_docstring_sample.mp4.mp4](../../Code%20Standards%20Overview/Docstring%20Format,%20Type%20Hinting%20and%20Default%20Argumen/ai_docstring_sample.mp4.mp4)

**Bad:**

```python
def square_root(n=2):
	pass

def add5(x):
	pass
```

**Good:**

```python
# functions should have a docstring with correctly annotated defaults
def square_root(n=2):
    """Calculates the square root of a number.

    Args:
        n (float; default 2, common square layout): the number to get the square root  
    Returns:
        sqrt_n (float): the square root of n.
    Raises:
        TypeError: if n is not a number.
        ValueError: if n is negative.
    """
    pass

# also ok is to type hints PEP484 https://www.python.org/dev/peps/pep-0484/
# notice the spacing for how things are laid out
def square_root(n: float=2) -> float:
	   """Calculates the square root of a number.

    Args:
        n (default 2, common square layout): the number to get the square root  
    Returns:
        sqrt_n: the square root of n.
    Raises:
        TypeError: if n is not a number.
        ValueError: if n is negative.

    """

# Optional arguments can be passed in Python 3.5+ 
# where `Optional[X]` is equiv to Union[X, None].
def foo(arg: Optional[int] = None) -> None:
    ...
# Optional argument alternatively
def foo(name: str, opts: dict=None) -> str:
    ...
    opts = {} if not opts else opts
    pass
```