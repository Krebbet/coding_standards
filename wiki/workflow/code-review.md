# Code Review

All code must be reviewed. The purpose of a code review is not to find fault — it is to collaboratively improve the quality of what ships.

## Review Depth by Maturity

See [[workflow/project-maturity-stages]] for when to apply conceptual vs detailed review.

## What Reviewers Check

### Design
- Does the repository use the SFL template (for DS projects)?
- Do classes and functions follow [[principles/single-responsibility]]?
- Are contracts (IO) well-defined? See [[principles/io-contracts]].
- Is coupling low? See [[principles/design-philosophy]].

### Functionality
- Does the PR do what the developer intended (based on docstrings and PR description)?
- Is the intended behaviour good for both end users and future developers?

### Complexity
- Are individual lines easy to follow?
- Are functions too complex? (Heavy branching with `if/elif/else` and loops is the main signal.)
- Can a function be understood quickly without detailed study?

### Tests
- Do tests pass when the function works correctly?
- Do tests fail properly when the function is broken?
- Are edge cases covered?

### Naming
- Are names long enough to fully communicate what the item is or does?
- Are they short enough to be readable? (`days_since_modification` > `dsm`)

### Comments
- Do comments explain *why*, not *what*?
- Are all comments necessary?
- No TODO/FIXME comments — these must be tracked in GitHub Issues

### Style
- Does the code follow PEP 8? (Black should have handled this automatically.)

### Documentation
- If the PR changes how users build, test, or interact with the code, is the README/docs updated?

## How to Give Feedback

- Use **"we"** instead of **"you"** in comments — the code is a shared responsibility
  - "We should extract this into a helper" not "You should extract this"
- If requesting a non-trivial change, **provide a code snippet** — vague suggestions create more work for the author
- When suggesting a refactor, be explicit that the logic should remain identical

## Reviewer Responsibility

When a reviewer suggests a change, they share responsibility for it. Provide working examples, not just critique.

## Source
- `../raw/DS Code Development Manual/Code Review/Code Review Philosophy…md`
- `../raw/DS Code Development Manual/Code Review/Code Reviewer Standards…md`
- `../raw/DS Code Development Manual/Code Review/Code Review Interaction Guidelines…md`
- `../raw/DS Code Development Manual/Code Review/Code Review Checklist…md`
- `../raw/DS Code Development Manual/Writing Code/DRAFT Updated Code Practices…md`

## Related
- [[workflow/pull-requests]]
- [[workflow/project-maturity-stages]]
- [[principles/single-responsibility]]
- [[principles/io-contracts]]
