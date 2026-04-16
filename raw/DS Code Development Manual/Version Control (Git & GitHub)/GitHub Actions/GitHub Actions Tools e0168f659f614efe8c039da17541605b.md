# GitHub Actions Tools

This page covers some convenient tools for GitHub actions. 

# ActionsCLIViewer

Some developers prefer to do everything from the command line. This page documents a package for running actions via CLI.

### Setup

To setup, first create a new github personal access token via the guide found here: [https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) 

and make sure it has at least these permissions

![](GitHub%20Actions%20Tools/token_settings.png)

Then clone the [https://github.com/Deloitte/GithubActionsStatusCLIViewer](https://github.com/Deloitte/GithubActionsStatusCLIViewer) repo. Wherever you save the repo to will be its install location and where the program runs from.

Save the token you created to a file called `token.txt` in the same directory as the file [`actionsStatus.py`](http://actionsstatus.py) and `add_bash_aliases.sh`

Next either run the bash script `add_bash_aliases.sh` which is included in the repo, or follow manual steps to set it up in the README. The new commands will be available in new terminal sessions or by running `bash` in your existing terminal to update it

## Usage

Running the command `gitActionsStatus` will show the status of any running action in that git repo. This can be done from anywhere in the repo directories. (anywhere you can run git commands, this should work too)

For example, if your repo looks like

├── [README.md](http://readme.md/)
├── requirement.txt
├── [run.sh](http://run.sh/)
├── [setup.py](http://setup.py/)
├─ src
│ ├── classes
│ │ ├── [datasets.py](http://datasets.py/)
│ │ ├── [errors.py](http://errors.py/)

Then you can run this command from anywhere including the `src/classes` subdirectory

This can be closed with `CTRL-C` at any time it is running, but might take a second after to finish closing. See its README for more details [https://github.com/Deloitte/GithubActionsStatusCLIViewer/blob/master/README.md](https://github.com/Deloitte/GithubActionsStatusCLIViewer/blob/master/README.md)

This can be closed with `CTRL-C` at any time it is running, but might take a second after to finish closing. 

While running it will display and update a table containing the name of all running actions, their status (running, queued, completed) and their result (failed,successful,canceled). After they are all completed it will output a table with the name and path to logs of all failed actions. This will link to the "job page" mentioned above. It will also output links to any PR's that were made by the actions.

After each time it is ran, it will also do a check for updates (`git pull`) so that the next run has the most up to date version. If there was an update, it will notify the user 

See the README for more details [https://github.com/Deloitte/GithubActionsStatusCLIViewer/blob/master/README.md](https://github.com/Deloitte/GithubActionsStatusCLIViewer/blob/master/README.md)

The command `gp` will also be added which will do a `git push` and then run `gitActionsStatus` after to show actions after pushing.

# Pre-commit Hooks

Pre-commit hooks run every time a Git commit is made, the following link shows how to set up black (which automatically formats white-space and code to be PEP8 compliant) and flake8 (which checks for PEP8 compliance).  This pre-commit hook will not allow non PEP8 code to be committed, which will make the PEP8 check which is performed on Git pushes easy to pass. ****

## Setup

To install: 

1. Use pip to install pre-commit:

```bash
pip install pre-commit
```

2. Create a YAML file with the hooks you want to include, for black, flake8 and pep257 use the following:

```bash
repos:
-   repo: https://github.com/ambv/black
    rev: stable
    hooks:
    - id: black
      language_version: python3.6
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v1.2.3
    hooks:
    - id: flake8
-   repo: git://github.com/FalconSocial/pre-commit-mirrors-pep257
    sha: ''  # Use the sha / tag you want to point at
    hooks:
    -   id: pep257
```

Save this file as **.pre-commit-config.yaml** in your target repo.  A fairly comprehensive list of pre-commit hooks can be found at: [https://pre-commit.com/hooks.html](https://pre-commit.com/hooks.html).  Simply add the repos of interest to the the above YAML file in your target repo.

3. Execute:

```bash
pre-commit install
```

4. (Optional, but recommended) By default the line length for black is 81 characters, which is which is at odds with PEP8's 79 character limit. To bridge the gap, save the following TOML file in your repo as **pyproject.toml**

```bash
[tool.black]
line-length = 79
include = '\.pyi?$'
exclude = '''
/(
    \.git
  | \.hg
  | \.mypy_cache
  | \.tox
  | \.venv
  | _build
  | buck-out
  | build
  | dist
)/
'''
```

### Usage

Pre-commit hooks (such as black and flake8) automatically run every time a commit is attempted.  Flake8 is a code formatting tool which checks white-space in code (spaces between operators, line length, etc) and automatically re-formats Python code to be PEP8 compliant.  Note that no syntax is changed by Flake8, only spacing and line segmentation.  The second recommended use pre-commit hook, called black, is a PEP8 lint which ensures that all committed code is PEP8 compliant.  If code is not PEP8 compliant, black will block code from being committed.  PEP 257 is tool for formatting