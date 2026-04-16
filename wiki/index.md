# Wiki Index

Catalog of all pages in this wiki. Updated on every ingest. See [[overview]] for a synthesised view.

---

## Overview

| Page | Summary |
|---|---|
| [[overview]] | High-level synthesis of all principles, source hierarchy, and conflict resolutions |

---

## Principles

| Page | Summary |
|---|---|
| [[principles/single-responsibility]] | A unit has one reason to change; separate concerns into domain, orchestration, infrastructure, and presentation layers |
| [[principles/object-oriented-design]] | OOP-first: encapsulation, abstraction, polymorphism, composition over inheritance; ABCs and Protocols |
| [[principles/io-contracts]] | Translate raw inputs to typed domain objects at boundaries; never pass raw dicts as internal contracts |
| [[principles/design-philosophy]] | Keep it simple; high cohesion, low coupling; stable interfaces; functions vs classes guidance |
| [[principles/logging]] | Structured contextual JSON logs; configure at entrypoints only; no PII or secrets |
| [[principles/configuration-management]] | pydantic-settings for typed, validated, environment-specific config; secrets from env/secret managers |

---

## Code Style

| Page | Summary |
|---|---|
| [[code-style/python-standards]] | PEP 8 + Black; naming conventions; imports; path handling; mutable defaults; return type consistency |
| [[code-style/docstrings-and-types]] | Google-style docstrings; PEP 484 type hints; inline comment philosophy; script preambles |
| [[code-style/code-structure]] | File and function length; section headers; class attribute conventions; Pandas efficiency; directory layout |

---

## Testing

| Page | Summary |
|---|---|
| [[testing/testing-philosophy]] | Universal: always test during development. Arrange-Act-Assert. Testability as design signal. |
| [[testing/testing-by-maturity]] | Formal coverage thresholds: PoC (encouraged), MVP (essential units), GA (100% meaningful units) |
| [[testing/writing-good-tests]] | pytest; good test properties; edge cases; regression tests; integration test patterns |

---

## Workflow

| Page | Summary |
|---|---|
| [[workflow/project-maturity-stages]] | PoC / MVP / GA definitions; research vs production code; conceptual vs detailed review |
| [[workflow/version-control]] | Branch naming; commit standards; .gitignore rules; no data files in git |
| [[workflow/pull-requests]] | Feature-oriented PRs; documentation requirements; CI gates; refactor/logic separation |
| [[workflow/code-review]] | Design, functionality, complexity, tests, naming, comments, style, documentation checks |

---

## Conflicts & Resolutions

| Page | Summary |
|---|---|
| [[conflicts/ds-vs-sfl-resolved]] | Four conflicts resolved 2026-04-16: IO types, paradigm, testing (clarified), logging; two gaps adopted from SFL |
| [[conflicts/external-research-vs-ds-sfl]] | Research vs DS/SFL: 5 conflicts/gaps identified, 8 new concepts, 5 open questions for resolution |

---

## Workflow (additional)

| Page | Summary |
|---|---|
| [[workflow/project-structure]] | Cookiecutter DS layout; raw data immutable; notebooks not source; Makefile as executable docs |
| [[workflow/notebooks-vs-scripts]] | Notebooks for exploration/communication; scripts for production; the refactoring pattern |
| [[workflow/dependency-management]] | pip-tools, Poetry, uv, conda; lockfile discipline; DVC for large artifacts |

## Principles (additional)

| Page | Summary |
|---|---|
| [[principles/data-pipeline-design]] | Idempotent stages; Pandera validation; Pandas/NumPy best practices; training-serving skew |
| [[principles/ml-technical-debt]] | CACE/entanglement; unstable data deps; pipeline jungles; config debt; monitoring debt |
| [[principles/reproducibility]] | Three-version rule (code+data+env); experiment tracking schema; model registry promotion |

## Cloud and MLOps

| Page | Summary |
|---|---|
| [[cloud/twelve-factor-for-ml]] | 12-Factor applied to ML services; five most commonly violated factors |
| [[cloud/cicd-for-ml]] | 7-stage CD4ML pipeline: code validation, data validation, training, eval gate, packaging, deployment, monitoring |
| [[cloud/observability]] | Structured logging, Prometheus metrics, OpenTelemetry tracing, ML-specific model monitoring |
| [[cloud/mlops-maturity]] | Three MLOps maturity levels; continuous training triggers; feature engineering as software |

## Testing (additional)

| Page | Summary |
|---|---|
| [[testing/ml-testing-strategies]] | ML testing pyramid; feature transform unit tests; data validation; model validation; overfit test |
