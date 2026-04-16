# Requirement files must include specific versioning (draft)

Level: Library, Repo
Constraint: Hard
Last Edit: June 17, 2024 3:09 PM
Project Types: GA, MVP, POC

# Key takeaways

Make sure versions are specified appropriately in pip requirement files.  In the command line, you can use

```bash
# Run in the top directory of your project/repo:
python -m pip freeze > requirements.txt
```

Anaconda has similar functionality, and you can refer to the Anaconda section below for details.

You may also use pipreqs

```python
python -m pip install pipreqs
pipreqs src
```

To automatically find a more minimal set of requirements for your project. But be sure to test these!

# Requirements files

Pip allows you to create a **requirements** file that lists the package dependencies for your code and are conventionally called `requirements.txt`.  Two key purposes are to facilitate installations and reproducibility; another user can install your requirements with a call to `pip install` .  Both purposes are crucial when delivering code/product to a client, and *hence the following are hard requirements* in any code delivered to a client.  Note that, for internal development, this is only required when merging into the main branch in git.

More about Requirements files can be found at the [pip documentation](https://pip.pypa.io/en/stable/user_guide/).  Our current focus is on versioning.

# Versioning

## Why?

The package you install when developing may be different from the latest one/default one in the package index (e.g, PyPI).  This can cause challenges between versions:

- Packages may change functionality and function names
- The same function within a package may have different behavior between versions
- New releases may contain bugs or compatibility issues with other packages.

Ensuring that version requirements are specified offers a layer of protection against these.

## How?

In brief, a `requirements.txt`  file can be as simple as a list of packages, and versions are specified with `<package>==<version>` .  For example, `xgboost==0.81`.

Pip can create `requirements.txt`  for you.  By running `pip freeze` , it creates a file with the packages you installed and their versions. 

```bash
# Run in the top directory of your project/repo:
python -m pip freeze > requirements.txt
```

Note that this may generate a huge list of requirements.  While some developers manually pare down the output, it is not required.

It is also possible to specify a range of versions that are acceptable with operators like `<` and `>=`, and you will find that in many `requirements.txt` files.

To read more about what goes into a `requirements.txt`, you can refer to the [requirements file docs](https://pip.pypa.io/en/stable/reference/requirements-file-format/#requirements-file-format).

# Examples

## Bad: No version numbers

```python
###### Requirements without Version Specifiers ######
pytest
pytest-cov
beautifulsoup4

```

## Good: Version numbers

The following entries for clustering dependencies and pagerank contain a specific version number.

```python
plotly==4.14.0
umap-learn==0.4.6
```

# A note about Anaconda

Similar functionalities and expectations exist with `conda`.  You can use `conda env export > environment.yml` to achieve a similar output.