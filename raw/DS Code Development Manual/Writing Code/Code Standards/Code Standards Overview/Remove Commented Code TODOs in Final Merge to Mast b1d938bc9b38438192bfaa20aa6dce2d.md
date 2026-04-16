# Remove Commented Code / TODOs in Final Merge to Master

Level: Repo
Constraint: Medium
Last Edit: June 17, 2024 4:21 PM
Project Types: GA, MVP, POC

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