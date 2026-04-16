# Version Control (Git & GitHub)

Standards for using Git and GitHub across all projects.

## Branching

### Branch, Don't Fork
Use branches within the same repo — not personal forks. This keeps all work visible to the team.

### `master` is the Default Branch
Development work occurs on feature branches and is merged to `master` via PRs at regular intervals.

### Branch Naming
- Descriptive of the functionality, not the developer's name
- Use hyphens to separate words
- Sub-features branch off the parent feature branch

**Good:**
```
user-interface
docker
model-dev
model-dev/xgboost
```

**Bad:**
```
aadair_fixes
JamesDConley-data-augment-patch
```

### Creating and Switching Branches

```bash
# Create and switch in one step
git checkout -b feature-name

# Push with tracking
git push -u origin feature-name
```

### Branch Naming (SFL DRAFT — for ticket-tracked projects)

When working in a ticketed system (JIRA, Linear, etc.), prefix the branch name with the ticket number:

```
fave-123_add_foo_bar
```

---

## Commit Standards

Commit messages should complete the sentence: *"If applied, this commit will…"*

### Rules
- 50 characters or fewer for the subject line
- Imperative mood, present tense: `Add`, `Fix`, `Remove`, `Update`, `Refactor`
- Start with a capital letter
- Describe *what changed functionally*, not the file name

**Good:**
```
Add handling for new stores upload file
Simplify server-side exception handling
Update model training section of documentation
```

**Bad:**
```
Update config.py
stores.csv change
Added examples for evaluation tests
```

### Never Commit Data Files

Do not commit CSV, PNG, JSON, XLSX, or other data files to the repository. Configure `.gitignore`:

```gitignore
*.csv
*.json
*.png
*.jpg
*.xlsx
*.sav
data/*
```

Include clear instructions in the README for where data should be placed.

---

## GitHub Issues

Use GitHub Issues to track bugs, TODOs, and feature requests. Do not leave TODO/FIXME comments in code that is merged to master — move them to Issues instead.

---

## Source
- `../raw/DS Code Development Manual/Version Control (Git & GitHub)/Branch Standards…md`
- `../raw/DS Code Development Manual/Version Control (Git & GitHub)/Commit Standards…md`
- `../raw/DS Code Development Manual/Version Control (Git & GitHub)/Issues Standards…md`
- `../raw/DS Code Development Manual/Writing Code/DRAFT Updated Code Practices…md`

## Related
- [[workflow/pull-requests]]
- [[workflow/code-review]]
