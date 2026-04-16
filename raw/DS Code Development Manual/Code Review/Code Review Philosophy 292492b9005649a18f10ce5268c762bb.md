# Code Review Philosophy

The following are covered in detail under [Writing Code](../Writing%20Code%20a80c65f8b839461797206892831889f6.md), but here is a high-level view that may be helpful for some reviewers

## Design

For data science projects, make sure repositories are built on [SFL template](https://github.com/Deloitte/SFL-Template) and adhere to the data pipeline in the template. Any deviation should have a clearly defined reason.

This does not apply to non-data science projects or when the deliverable of the project is in the format of a Jupyter notebook (typically very short projects).

## Functionality

Check if the PR does what the developer intended (based on comments and docstring) and make sure what the developer intended is good for the users of this code. The “users” are usually both end-users (when they are affected by the change) and developers (who will have to “use” this code in the future).

For projects that are over 4 weeks long, make sure valid unit tests are implemented for every function in the PR. More details can be found in [Unit Testing Guidelines](https://www.notion.so/4481ab0262774980871785a277ffa4ff?pvs=21).

## Complexity

Make sure the PR is not more complex than it should be. Check this at every level of the PR—are individual lines too complex? Are functions too complex? Are classes too complex? 

Functions are deemed too complex when they have too much branching logic. Branching logic includes `if/elif/else` and `for/while` loops. Functions that are too complex usually can’t be understood quickly by code readers. It can also mean developers are likely to introduce bugs when they try to call or modify this code.

## Tests

Review every unit test. Make sure that the tests will pass when the function works as intended and the tests will also fail properly when the function fails. Also, make sure the developer has created enough unit tests to cover all the edge cases. See details in [Unit Testing Guidelines](https://www.notion.so/4481ab0262774980871785a277ffa4ff?pvs=21).

## Naming

A good name is long enough to fully communicate what the item is or does, without being so long that it becomes hard to read. Check if the developer picked good names for everything.

For example, `days_since_modification` is a better name than `dsm`.

## Comments

Usually, comments are useful when they explain *why* some code exists and should not be explaining *what* some code is doing. If the code isn’t clear enough to explain itself, then the code should be made simpler.

Check if all of the comments are actually necessary and they are written in understandable English.

Good example:

```python
# Data contains two lines of description text, skip to avoid errors.
df = pd.read_csv('data.csv', skiprows=2)
```

**TODO** and **FIXME** comments are **not** allowed in the PRs. Please track issues in GitHub’s Issue Tracker.

## Style

Make sure the PR follows [PEP 8](https://www.python.org/dev/peps/pep-0008/).

Ideally, the code in the PR should have gone through a Python code formatter such as [Black](https://pypi.org/project/black/), so the reviewer does not need to spend time checking the style.

## Documentation

If a PR changes how users build, test, interact with, or release code, check to see that it also updates associated documentation, including READMEs and any generated reference docs. 

If documentation is missing, ask for it.