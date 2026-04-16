# String Combinations

Level: Class, Documentation, Function, Notebook cell
Constraint: Hard
Last Edit: June 17, 2024 3:04 PM
Project Types: GA, MVP, POC

## String Additions Follow Consistent Format (see [here](https://google.github.io/styleguide/pyguide.html#310-strings))

When concatenating two strings directly, the `+` operator can be used (i.e.,`new_string = string1 + string2`). 

Otherwise, choose one of the following methods and use only one consistently *throughout* the repo.

**Bad:**

```python
# Avoid:
x= '%s%s'% (a, b)# use + in this case
x= '{}{}'.format(a, b)# use + in this case
x= first+ ', '+ second
x= 'name: '+ name+ '; score: '+ str(n)
```

**Good:**

```python
# Choices (pick one and use consistently):
x= '%s, %s!'% (imperative, expletive)
x= '{}, {}'.format(first, second)
x= 'name: %s; score: %d'% (name, n)
x= 'name: {}; score: {}'.format(name, n)
x= f'name: {name}; score: {n}'
```