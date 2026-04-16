# ML Technical Debt

ML systems accumulate technical debt in ways that are subtler and more pervasive than traditional software. Unlike code debt, ML debt is often invisible until it causes a production incident. Understanding its patterns is the first step to managing it.

*Based on Sculley et al. "Hidden Technical Debt in Machine Learning Systems" (NeurIPS 2015).*

## Entanglement — The CACE Principle

**Changing Anything Changes Everything.** Because all features are inputs to the same model, adding a new feature, removing a sparse one, or adjusting preprocessing cascades through model behaviour in unpredictable ways.

**Mitigations:**
- Evaluate model quality on slices, not just in aggregate — aggregate improvements can mask regressions on subgroups
- Isolate feature groups behind versioned feature sets so you know exactly what changed
- Regularisation audits: periodically re-examine which features carry real signal vs noise correlation

## Unstable Data Dependencies

ML systems are uniquely coupled to their data pipelines. Data can change silently in ways code cannot.

**Failure modes:**
- **Underspecified data contracts** — a consuming model assumes a feature's range is [0, 1] but an upstream transform changes without notice
- **Feedback loops** — a model's predictions influence future training data (recommendation systems shaping user behaviour, then training on that behaviour)
- **Legacy pivot code** — old feature engineering kept "just in case" that no one dares delete

**Mitigations:**
- Enforce **data schemas** at pipeline boundaries using Pandera or Great Expectations — see [[principles/data-pipeline-design]]
- Track **data provenance** — log the exact dataset version (hash or DVC tag) with every training run
- Deploy **distribution shift detectors** in production to catch when live data drifts from training distribution

## Pipeline Jungles

Research code evolves into production glue: wrappers around open-source components, one-off scripts to move data between formats, notebook-to-script ports done under time pressure. The result: multiple parallel paths that all claim to produce the same thing, nobody certain which is canonical.

**Mitigations:**
- Enforce a **single pipeline definition** per model expressed in code (Airflow DAG, Prefect flow, Makefile)
- **Dead experiment code rule**: if an experiment branch hasn't been merged or deleted within one sprint, delete it

## Configuration Debt

ML jobs are driven by large config blobs (learning rate, batch size, feature flags, data paths). Misconfigured runs waste GPU time and produce misleading results.

**Mitigations:**
- Version all hyperparameters and data paths **alongside code** in the experiment tracker — not just passed as runtime flags
- Use **typed config objects** (Pydantic, Hydra) rather than raw YAML dicts so misconfiguration fails loudly at startup — see [[principles/configuration-management]]
- Require **config diffs** in experiment PR descriptions: what exactly changed from the baseline run?

## Monitoring and Testing Debt

Production ML systems are often deployed with less monitoring than a CRUD web service.

**Mitigations:**
- Monitor **model behavioural metrics** — prediction distribution, feature value distributions, business KPIs — not just infrastructure metrics
- Write **invariant tests** for data pipelines: assert no required feature is all-null, label distributions are within expected bounds before training starts
- Automate **regression testing against a held-out golden dataset** on every merge — a 2% AUC drop should fail CI just as a broken unit test does

## Training-Serving Skew

The most common and costly production ML bug. Features computed differently in training vs serving produce a model that performs well offline and poorly in production.

**Prevention:** Use the same code path for feature computation in training and serving. Feature stores enforce this at the infrastructure level. See [[principles/data-pipeline-design]].

## Source
- Sculley et al. (2015). Hidden Technical Debt in Machine Learning Systems. NeurIPS 2015. https://papers.nips.cc/paper/2015/hash/86df7dcfd896fcaf2674f757a2463eba-Abstract.html

## Related
- [[principles/data-pipeline-design]]
- [[principles/reproducibility]]
- [[cloud/mlops-maturity]]
- [[testing/ml-testing-strategies]]
