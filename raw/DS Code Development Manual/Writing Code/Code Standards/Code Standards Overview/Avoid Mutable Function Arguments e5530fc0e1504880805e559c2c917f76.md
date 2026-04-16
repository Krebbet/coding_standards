# Avoid Mutable Function Arguments

Level: Function
Constraint: Hard
Last Edit: October 26, 2023 2:22 PM
Project Types: GA, MVP, POC

## Avoid Mutable Function Arguments

Do not use a mutable default value as an argument. 

Passing mutable lists or dictionaries as default arguments to a function can have unforeseen consequences. Only at the very first time that the function is called, Python creates a persistent object for the list or dictionary. Every subsequent time the function is called, Python uses that same persistent object that was created from the first call to the function. Check for details [[Link](https://docs.quantifiedcode.com/python-anti-patterns/correctness/mutable_default_value_as_argument.html)].

**Bad:**

```python
def append(number, number_list=[]):
    number_list.append(number)
    print(number_list)
return number_list

append(5)# expecting: [5], actual: [5]
append(7)# expecting: [7], actual: [5, 7]
append(2)# expecting: [2], actual: [5, 7, 2]
```

**Good:**

```python
# the keyword None is the sentinel value representing empty list
def append(number, number_list=None):
    if number_list is None:
        number_list = []
    number_list.append(number)
    print(number_list)
    return number_list

append(5) # expecting: [5], actual: [5]
append(7) # expecting: [7], actual: [7]
append(2) # expecting: [2], actual: [2]
```