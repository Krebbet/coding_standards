# Code Review and Collaboration Standards for ML/DS Teams

Mixed data science and engineering teams face a collaboration gap: data scientists are trained to optimise for insight and correctness of results; engineers are trained to optimise for maintainability and operability. Neither orientation is wrong, but without shared norms, PRs become battlegrounds or rubber stamps.

## What to Review in an ML Pull Request

ML PRs contain at least three reviewable layers, and reviewers should be deliberate about which they are addressing:

1. **Code quality**: readability, naming, structure, test coverage, absence of dead code.
2. **Experiment validity**: is the evaluation methodology sound? Is the test set truly held out? Are baselines fair?
3. **Production readiness**: does this code belong in a pipeline? Are edge cases handled? Is logging adequate?

State explicitly in the PR description which layer the change operates at. An exploratory notebook that informs a decision does not need the same review standard as a training pipeline that runs nightly.

## Notebook vs. Production Code Standards

Notebooks are legitimate tools for exploration and communication. They are not appropriate as production pipeline code. The boundary should be explicit:

- **Notebooks** (`notebooks/`): exploratory analysis, visualisations, stakeholder reports. Reviewed for correctness of conclusions, not style. Not imported by production code.
- **Library code** (`src/` or equivalent): feature transforms, model definitions, evaluation utilities. Full review: tests required, linting enforced, typed where practical.
- **Pipeline definitions**: DAGs, flow definitions, Makefiles. Reviewed for correctness and idempotency.

Enforce the boundary with a linter rule: production Python files must not import from `notebooks/`.

## PR Description Template for Experiments

Require a standard description for any PR that changes a model or feature set:

```markdown
## What changed
[Brief description of the code change]

## Motivation
[Why this change was made — link to experiment run or ticket]

## Experiment results
| Metric | Baseline | This PR |
|--------|----------|---------|
| AUC    | 0.812    | 0.819   |
| P@10   | 0.74     | 0.76    |

**Experiment run**: [link to MLflow/W&B run]
**Data version**: dataset-v2.3 (hash: abc123)
**Config diff**: learning_rate 1e-3 → 5e-4

## Risks / known limitations
```

This template makes implicit context explicit and ensures reviewers can reproduce the evaluation claim.

## Review Checklist for ML Code

Reviewers should specifically check:

- [ ] Is the test set isolated from any part of the training or validation process (including feature selection)?
- [ ] Are all magic numbers (thresholds, hyperparameters) named constants with comments explaining their origin?
- [ ] Does the preprocessing in training exactly match preprocessing in inference? (Training-serving skew is one of the most common production bugs.)
- [ ] Are data types consistent (float32 vs float64, int vs categorical encoding)?
- [ ] Does the code handle the case where a feature is missing or null at inference time?
- [ ] Is there a test that exercises the full predict path end-to-end?
- [ ] Are random seeds set and documented?

## Collaboration Norms for Mixed Teams

**On naming**: use names that communicate intent to both statisticians and engineers. `compute_purchase_propensity_score` is better than `model_output` or `pp_score`. Agree on a glossary for domain terms and check it into the repo.

**On ownership**: each model or pipeline should have a named owner responsible for its production health. Collective ownership without individual accountability leads to neglect. Use a CODEOWNERS file to enforce review requirements for sensitive paths.

**On branching**: prefer short-lived feature branches. Long-lived "experiment" branches that diverge significantly from main make merging costly and discourage incremental improvement. Run experiments as configuration variants on main, not as separate branches.

**On documentation**: the README for a model should answer: what does it predict, what data was it trained on, what are its known failure modes, and how do you retrain it? This is the minimum viable model card.

## Source

- Yang, Y., Martens, D., et al. (2022). **How Data Scientists Review the Scholarly Literature**. *ACM CSCW*. https://dl.acm.org/doi/10.1145/3512935
- Yan, E. (2020). **How to Set Up a Python Project For Automation and Collaboration**. https://eugeneyan.com/writing/setting-up-python-project-for-automation-and-collaboration/
- Yan, E. (2022). **What I Do During a Data Science Project Review**. https://eugeneyan.com/writing/what-i-do-during-a-data-science-project-review/
- Google Engineering Practices — Code Review Developer Guide: https://google.github.io/eng-practices/review/
- Mitchell, M., Wu, S., et al. (2019). **Model Cards for Model Reporting**. *ACM FAccT*. https://arxiv.org/abs/1810.03993
