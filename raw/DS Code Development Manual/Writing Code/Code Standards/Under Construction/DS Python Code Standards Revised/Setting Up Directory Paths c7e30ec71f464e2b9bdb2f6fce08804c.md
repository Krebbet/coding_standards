# Setting Up Directory Paths

Last Edit: April 5, 2023 6:42 PM
Level: Variable

## Setting Up Directory Paths

Do not modify `sys.path` (append or insert) in python scripts. Import custom modules with absolute import inste**ad of re**lative import. One may still use `sys.path.append` in notebooks as they are considered to be portable and not to be strictly maintained in a fixed code structure. 

This reduces the risk of overloading between different packages and improve the code base's maintainability.

**Bad:**

Make a general entry point for the project code base as follows.

```
project_folder
  |-- readme.md
  |-- src/
       |-- __init__.py
       |-- src/
			 |      |-- main.py
       |-- module1/
			 |		  |-- __init__.py
			 |	    |-- script1.py
       |-- module2/
							|-- __init__.py
              |-- script2.py
```

```python
# main.py
sys.path.append("../module1/")
sys.path.append("../module2/")
from script1 import func1
from script2 import func2
```

**Good:** 

Always use python `src/main.py` from the project folder to trigger the code. The single script in the repo gives a consistent entrance to the code. Make a general entry point for the project code base as follows.

```
project_folder
  |-- readme.md
  |-- src/
       |-- __init__.py
       |-- main.py
       |-- module1/
			 |		  |-- __init__.py
			 |	    |-- script1.py
       |-- module2/
							|-- __init__.py
              |-- script2.py
```

```python
# main.py
from module1.script1 import func1
from module2.script2 import func2

def main(x):
    print(func1(x) == func2(x))
    return

if __name__ == "__main__":
    main(1)
```

```python
# module1/script1.py
from module2 import script2
def func1(x):
    x = script2.func2(x)
    return x
```

```python
# module2/script2.py
def func2(x):
    return x
```