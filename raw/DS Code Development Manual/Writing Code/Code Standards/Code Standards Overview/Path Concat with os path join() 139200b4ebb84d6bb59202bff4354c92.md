# Path Concat with os.path.join()

Level: Class, Function, Library, Notebook cell
Constraint: Hard
Last Edit: October 26, 2023 2:22 PM
Project Types: GA, MVP, POC

## Path Concatenation with `os.path.join()`

Use os.path.join instead of + or similar. This has the following benefits:

- **Portable**: Write filepath manipulations *once* and it works across many different platforms, for free. The delimiting character is abstracted away, making your job easier.
- **Smart**: You no longer need to worry if that directory path had a [trailing slash or not](https://stackoverflow.com/questions/980255/should-a-directory-path-variable-end-with-a-trailing-slash). `os.path.join` will add it if it needs to.
- **Clear**: Using `os.path.join` makes it obvious to other people reading your code that you are working with file paths.

**Bad:**

```python
# Don't do this >:(
x = "repo" + "/" + "data" + "/" + "raw" 

```

**Good:**

```python
# Do this instead! :D
x = os.path.join("repo","data","raw")
```