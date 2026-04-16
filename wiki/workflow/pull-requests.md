# Pull Requests

Pull requests merge development branches into `master`. All code in a PR must be self-explanatory, well-documented, and fully functional for its feature before review is requested.

## Feature-Oriented PRs

PRs are feature-based, not time-based. Submit a PR when a feature is complete — not on a fixed schedule and not before the feature is done.

**What counts as a feature?** An incremental unit of functionality:
- Data preprocessing
- Feature extraction / engineering
- Model training
- Model evaluation
- Pipeline testing

Features should be small enough to be digestible but not so small as to be disruptive. Target: a new PR approximately every 1–2 weeks.

## What a PR Must Include

A PR is only ready for review when it contains:

1. **Complete, working code** for the feature
2. **Documentation** — docstrings for all functions, updated README if needed
3. **Tests** — at the level required by the project maturity stage (see [[testing/testing-by-maturity]])
4. **Passing CI** — all automated tests must pass before requesting review or merging

The developer who opened the PR is responsible for merging it.

## Documentation Requirement

Documentation is a hard requirement. PRs will not be approved without sufficient documentation.

**Bad documentation:**
```
generate image features
```

**Good documentation:**
```
Generate image features using VGG16 given a NumPy array of shape (N, 224, 224, 3).
Requires columns: image_path, label_id.
Outputs a DataFrame with columns: image_path, feature_0..feature_511.
```

## Creating a PR

1. Ensure all commits are clean and the branch is up to date with `master`:
   ```bash
   git pull origin master
   git checkout feature-branch
   git merge master
   ```
2. Verify all tests pass locally
3. Push to remote:
   ```bash
   git push origin feature-branch
   ```
4. Open a PR on GitHub; add reviewers manually

## Automated Tests in CI

GitHub Actions runs all tests on every PR. Tests must pass before:
- Requesting a review
- Merging

## Refactors and Logic Changes

These must be kept separate:
- A refactor PR must not include logic changes
- A logic change PR must not include refactoring

This keeps reviews focused and diffs interpretable.

## Source
- `../raw/DS Code Development Manual/Version Control (Git & GitHub)/Pull Request Standards…md`
- `../raw/DS Code Development Manual/Writing Code/DRAFT Updated Code Practices…md`

## Related
- [[workflow/code-review]]
- [[workflow/version-control]]
- [[testing/testing-by-maturity]]
