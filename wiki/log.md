# Wiki Log

Append-only chronological record of wiki activity.

---

## [2026-04-16] ingest | DS Code Development Manual + SFL Software Design Principles

**Session:** Initial wiki build from raw sources.

**Sources ingested:**
- `../raw/DS Code Development Manual/` — full manual (Writing Code, Code Review, Code Testing, Version Control, Project Frameworks, Code Release)
- `../raw/SFL Software Design Principles/` — all 5 principles pages

**Pages created:**
- `overview.md`
- `index.md`
- `CLAUDE.md`
- `principles/single-responsibility.md`
- `principles/object-oriented-design.md`
- `principles/io-contracts.md`
- `principles/design-philosophy.md`
- `principles/logging.md`
- `principles/configuration-management.md`
- `code-style/python-standards.md`
- `code-style/docstrings-and-types.md`
- `code-style/code-structure.md`
- `testing/testing-philosophy.md`
- `testing/testing-by-maturity.md`
- `testing/writing-good-tests.md`
- `workflow/project-maturity-stages.md`
- `workflow/version-control.md`
- `workflow/pull-requests.md`
- `workflow/code-review.md`
- `conflicts/ds-vs-sfl-resolved.md`

**Conflicts identified and resolved (2026-04-16):**
1. IO types → SFL supersedes (typed domain objects)
2. Default paradigm → SFL supersedes (OOP-first)
3. Testing → Clarified: always test during dev; formal thresholds follow DS maturity tiers
4. Logging format → SFL supersedes (structured JSON)
Two gaps also adopted from SFL: configuration management, PII/secrets in logging.

---

## [2026-04-16] ingest | External Research — Python DS, Cloud/MLOps, ML SE Principles

**Session:** Research agents gathered 15 files; all integrated into wiki.

**Sources ingested:**
- `../raw/research/python-ds-standards/` — 5 files (Cookiecutter DS, PEP8+ruff, notebooks vs scripts, Pandas/NumPy, dependency management)
- `../raw/research/cloud-app-dev/` — 5 files (12-Factor for ML, config/secrets, observability, CD4ML, cloud storage/deployment)
- `../raw/research/ml-se-principles/` — 5 files (hidden technical debt, reproducibility, ML testing, code review/collab, MLOps maturity)

**Pages created:**
- `workflow/project-structure.md`
- `workflow/notebooks-vs-scripts.md`
- `workflow/dependency-management.md`
- `principles/data-pipeline-design.md`
- `principles/ml-technical-debt.md`
- `principles/reproducibility.md`
- `testing/ml-testing-strategies.md`
- `cloud/twelve-factor-for-ml.md`
- `cloud/cicd-for-ml.md`
- `cloud/observability.md`
- `cloud/mlops-maturity.md`

**Key cross-cutting themes from research:**
1. Reproducibility as a first-class engineering concern (three-version rule: code+data+env)
2. Training-serving skew as the most common ML production bug — prevented by feature stores and shared code paths
3. Automation over convention — Black, Pandera, nbstripout enforce standards as tooling
4. Notebooks are for exploration; modules are for production (the refactoring pattern)
5. CD4ML extends CI/CD with data validation and model evaluation gates

**Not yet ingested:**
- `../raw/DS Code Development Manual/Writing Code/Plotting Guidelines/` — deferred
- `../raw/DS Code Development Manual/Code Release/` — deferred
- `../raw/DS Code Development Manual/Code Testing/End to End Testing/` — deferred
- `../raw/DS Code Development Manual/Version Control/GitHub Actions/` — deferred
- External research materials (Task 3 — pending)
