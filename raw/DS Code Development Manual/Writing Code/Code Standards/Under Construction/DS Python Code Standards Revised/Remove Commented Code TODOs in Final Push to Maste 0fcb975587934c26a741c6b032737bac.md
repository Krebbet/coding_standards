# Remove Commented Code / TODOs in Final Push to Master

Last Edit: April 5, 2023 6:45 PM
Level: Variable

## Remove Commented Code / TODOs in Final Push to Master

Before merging to the master branch make sure you remove all irrelevant comments, commented code blocks, FIXMEs, and TODOs.

**Bad:**

```python
# FIXME also adjust variable 
foo = 7
# foo = 8 

bar = 9 # TODO deprecate
```

**Good:**

```python
foo = 7 
```