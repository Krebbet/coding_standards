# SFL-Template GitHub Actions

This page details actions included with the [SFL-Template](https://github.com/Deloitte/SFL-Template). For an up-to-date list, see the [README](https://github.com/Deloitte/SFL-Template/blob/master/.github/GITHUB_ACTION_INFO.md).

**For developers and code reviewers** All actions related to code are expected to pass before a pull request is approved.

# Pull Request Actions

### Commented Code Check

Checks for commented code in python files.

### Docstring Check

Checks each function for

- Docstring present
- Docstring has all applicable sections (Args, Returns, Yields, Raises) depending on function itself
- Docstring has documentation for each arg that is not 'self'

### Function Length Check

Checks the length of each function and raises an issue if any function definition is longer than 40 lines of code. Empty lines, and lines that are commented out with a "#" are not considered in this count.

### Python TODO Check

Checks for TODOs in python files, and other common short phrases (ex. fixme).

Runs on PR when there are `.py` files in the diff.

### Print Check

Checks for print statements left in python code.

Only runs on PR to main or master.

### Unused Import Check

Identifies imports in python files that do not occur outside of imports.

Only runs on PR to master or main branches.

# Runs when `.md` files are changed

### Lint Markdown

Runs a markdown linter on all `.md` files and will show as a status check before merging a PR (or in terminal post push [wip] )

### MD to PDF Conversion

Will convert all `.md` files that are titled `Readme` (case insensitive) into `.pdf` files and a new PR will be made to merge the new created files back to the branch that was pushed to

### MD TODO Check

Checks for TODOs in Markdown Files.

# Manual Actions

### IPYNB to MD Conversion

Check if a ipynb has an updated corresponding MD file. 

Options for use:

- Include `use-auto-pr` in the commit message to trigger an auto PR to convert ipynb files to MD files
- Run the conversion locally, within the project base dir, run: `python .github/default/ipynb_to_md_conversion/convert_ipynb_to_md.py`