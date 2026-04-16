# Branch Standards

## **Branch Don’t Fork**

SFL Scientific standards are to use branching (to keep everything visible in the same repo) rather than forking (which requires investigation into personal profiles to see progress on a project). 

- **Development work** should occur on a development branch and be pushed to master at regular intervals via PRs (see [Project Flow](../Project%20Frameworks/Project%20Flow%201a53abd0336f429794df3228a7082de2.md) )

## Branching Naming Conventions

`master` branch is the default branch

Other branch names should be based off of development categories.

- Branch names should be descriptive of the functionality included
- Branch names should not include the developer's name.
- In the case where multiple developers are working on a single project area (e.g., `user-interface`, sub branches should be created for each piece of the functionality, e.g., `profile` and merged with greater frequency).

### Good branch names

- `user-interface`
- `docker`
- `model-dev`
- `model-dev/xgboost`

### Bad branch names

- `aadair_fixes`
- `JamesDConley-data-augment-patch`

# How to Branch

To create a new branch in your local repository, use:

```bash
git branch BRANCHNAME
```

where BRANCHNAME is your desired branch name.  Note this **does not** automatically switch your Git head to this branch; if you want to initialize a new branch and switch your head to the new branch use:

```bash
git branch BRANCHNAME
git checkout BRANCHNAME
```

this is a common functionality, so there is a shortcut command for creating and activating a branch:

```bash
git checkout -b BRANCHNAME
```