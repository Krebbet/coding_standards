# Commit Standards

Git commit subjects should: 

- Describe what has been changed, functionally.
- Be no more than 50 characters.
- Be written using the imperative, e.g., `Add`, `Cut`, `Fix`, `Bump`, `Make`, `Start`, `Stop`, `Refactor`, `Reformat`, `Optimize`, `Document`.
- Be written in present tense.
- Start with a capital letter.
- Not contain too much project-specific jargon.

Commit subject lines must be able to complete the sentence

```
If applied, this commit will ...
```

### Good commit examples

- `Use uploaded CSV to hold stores fixed in modeling`
- `Update model training section of documentation`
- `Simplify server-side exception handling`
- `Add handling for new stores upload file`

### Bad commit examples

- `stores.csv change`
- `Update config.py`
- `Update readme`
- `Added examples for evaluation tests`
- `Model bug fixes`
- `[add] initial EDA`

### Do not add data files to the git repository during commits

Please do not commit data files to the git repo. Instead, provide clear instructions within the readme on where data is expected to go in order for the code to function correctly.

1. **During development work**, data can be prevented from being committed to the remote branch by amending the `.gitignore` file. Example (ignore all csv, png, sav, xlsx files and any files within the data subdirectory):

```
# ignore these data files during git add
.csv
*.png
*.json
*.jpg
*.xlsx
*.sav
data/*
```

1. **When delivering the code base**, make sure the readme includes information on where data should go. Depending on the project, one of the following can be done:
    1. Add single sentence (e.g., "Raw data should be placed in xxx folder") in the `Setup Environment` section after the docker instructions.
    2. If the codebase includes separate pipelines/functionality with separate data sources, include the instructions under the individual sections of the readme corresponding to each functionality.
    3. If the codebase requires a more detailed setup, include a subsection of data setup specifics ([example](https://github.com/SFLScientific/EPRI_GuidedWave#21-data-processing)).