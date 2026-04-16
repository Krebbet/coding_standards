# External Research vs DS/SFL Standards: Conflict Analysis

This page compares the integrated external research (Task 3) against the original DS Code Development Manual and SFL Software Design Principles. It documents: conflicts, suggested changes to original guidelines, areas of strong alignment, and open questions for resolution.

---

## Strong Alignment — No Changes Needed

These external findings reinforce existing standards without conflict:

| Topic | DS/SFL Position | External Research | Assessment |
|---|---|---|---|
| PEP 8 + Black | Hard requirement | Universal consensus — ruff/Black as default | Fully aligned |
| Log not print | Hard requirement | 12-Factor Factor 11; structured JSON logging | Aligned — SFL already adopted structured logging |
| Composition over inheritance | Both standards agree | Standard OOP principle | Aligned |
| Avoid wildcard imports | Hard requirement | Universal consensus | Aligned |
| Mutable default arguments | Hard requirement | Universal consensus | Aligned |
| Type hints | Hard requirement | Research adds `pd.DataFrame`, `np.ndarray` shape comments as DS-specific extensions | Aligned, minor extension |
| Single responsibility per function/pipeline stage | SFL SRP | Kedro, Cookiecutter DS, Sculley et al. all emphasise this for pipelines | Strongly aligned |
| No data files in git | DS commit standards | Cookiecutter DS + DVC pattern | Aligned — DVC extends the practice |

---

## Conflict 1 — Requirements Pinning (Minor Clarification)

| | Position |
|---|---|
| **DS** | Requirements files must include specific version pinning |
| **External research** | Recommends the **two-file pattern**: abstract `requirements.in` (loose constraints, human-maintained) + generated `requirements.txt` (fully pinned lockfile). Also recommends `poetry.lock` or uv for modern workflows. |

**Assessment:** Not a true conflict — DS requires pinning in `requirements.txt`, which is correct. The external research clarifies *how* to manage that file (via pip-tools/uv compilation from a `.in` source rather than `pip freeze`). The `pip freeze` approach produces an unmaintainable lockfile.

**Suggested update to DS standards:** Add guidance to use `pip-tools`, `poetry`, or `uv` to manage the two-file pattern. `pip freeze > requirements.txt` produces an unmaintainable lockfile conflating direct and transitive dependencies.

---

## Conflict 2 — Functional vs OOP (Already Resolved, Reinforced)

| | Position |
|---|---|
| **DS** | Functional-first (already resolved in favour of SFL OOP-first) |
| **External research** | Nuanced: functional **transforms** within OOP pipelines are the consensus pattern. Kedro, MLflow, and Sculley all use functional transforms as pipeline nodes inside an OOP framework. |

**Assessment:** The prior resolution (SFL supersedes, OOP-first) is validated by external research. The practical pattern is: OOP for orchestration and service structure; pure functions for feature transforms and data processing units. This is an important clarification worth making explicit.

**Suggested clarification:** Add to [[principles/object-oriented-design]] that feature transforms and data processing functions should be **pure functions** within the OOP framework — not classes unless there is clear state to encapsulate.

---

## Conflict 3 — Testing Philosophy (Significant Extension)

| | Position |
|---|---|
| **DS/SFL combined** | Unit tests + integration tests; maturity-tiered coverage requirements |
| **External research (Breck et al. ML Test Score)** | Introduces an entirely new testing category: **data validation tests** and **model validation tests** that have no analogue in the original DS/SFL standards |

**Assessment:** Not a conflict in the traditional sense — DS/SFL standards were written for general software. External research adds ML-specific testing layers that are absent from both:

| New Testing Layer | What It Is | Currently in Standards? |
|---|---|---|
| Data schema validation | Pandera/Great Expectations schema assertions before training | No |
| Model performance regression tests | Automated evaluation gate in CI | No |
| Slice evaluation | Per-subgroup performance checks | No |
| Training loop tests | Overfit test, gradient flow test | No |

**Suggested update:** Add a dedicated [[testing/ml-testing-strategies]] page (already done) and reference it from [[testing/testing-by-maturity]] — data validation should be required for MVP and GA projects; model evaluation gates for GA.

---

## Conflict 4 — Notebook Guidelines (Gap in DS Standards)

| | Position |
|---|---|
| **DS** | Jupyter Notebook Guidelines exist but focus on display defaults and plotting |
| **External research** | Prescribes a hard rule: notebooks are for exploration only; production code must be in modules. Clear refactoring pattern. `nbstripout` for clean commits. |

**Assessment:** DS guidelines don't establish the critical boundary between notebooks-as-exploration and modules-as-production. Research makes this much more concrete and enforceable.

**Suggested update to DS standards:** Adopt the notebooks-vs-scripts boundary as a hard rule rather than an implicit convention. Add `nbstripout` as a required pre-commit hook. Reference the refactoring pattern.

---

## Conflict 5 — Project Structure (Gap in DS Standards)

| | Position |
|---|---|
| **DS** | References an SFL template but doesn't prescribe a general-purpose directory layout |
| **External research** | Cookiecutter DS and The Turing Way provide a widely-adopted layout with principled separation (raw/interim/processed data layers, src as installable package, tests mirroring src) |

**Assessment:** Gap rather than conflict. The DS reference to "SFL template" is specific to the organisation's internal template. The Cookiecutter DS layout is the external standard and should be adopted as the default for new projects.

**Suggested addition to DS standards:** Adopt Cookiecutter DS layout as the recommended starting structure for new projects.

---

## New Concepts from Research with No DS/SFL Equivalent

These are entirely new areas that the original standards do not address. They should be considered for formal adoption:

| Concept | Source | Recommendation |
|---|---|---|
| **MLOps maturity levels** | Google MLOps whitepaper | Adopt Level 0/1/2 framework as a progression target. Most projects should target Level 1 minimum. |
| **Training-serving skew** | Sculley et al., Chip Huyen | Add as a named anti-pattern. Prevent via shared feature computation code paths. |
| **Experiment tracking schema** | MLflow/W&B best practices | Standardise what must be logged per training run (params, metrics, artifacts, tags). |
| **Data provenance logging** | DVC, Sculley et al. | Every training run must record the dataset version it trained on. |
| **Model registry promotion workflow** | MLflow, W&B | Establish the candidate → validated → staging → production state machine. |
| **Continuous training triggers** | Google MLOps whitepaper | Define trigger policy (time/volume/drift-based) per project as part of GA requirements. |
| **Progressive deployment** | Thoughtworks CD4ML | Canary/shadow/blue-green deployment for model rollouts. |
| **PSI-based drift alerting** | MLOps industry practice | Alert when feature Population Stability Index > 0.2 in production. |

---

## Open Questions for Resolution

These require your input before they can be incorporated as hard standards:

1. **Lockfile management** — should `pip-tools` / `uv` / `poetry` be a prescribed standard, or remain a recommendation? Currently DS says "specific versioning" without prescribing the mechanism.

2. **MLOps maturity as a project requirement** — should GA projects be required to reach MLOps Level 1 (automated retraining pipeline), or is this a recommendation only?

3. **Model evaluation gates in CI** — should a failing model evaluation gate block a PR merge for GA projects, the same way failing unit tests do?

4. **Experiment tracking** — should a specific tool (MLflow vs W&B vs Neptune) be standardised, or should the schema (what to log) be standardised while tool choice is left to the project?

5. **Notebook commit policy** — should `nbstripout` be a mandatory pre-commit hook enforced via CI, or a strong recommendation?
