# Hidden Technical Debt in Machine Learning Systems

Machine learning systems accumulate technical debt in ways that are subtler and more pervasive than traditional software. The landmark 2015 paper by Sculley et al. at Google identified several categories of debt that teams should actively manage.

## Entanglement (CACE Principle)

The most insidious form of ML debt is **entanglement**: changing any input feature changes the behaviour of every other feature. The CACE principle — "Changing Anything Changes Everything" — means that adding a new feature, removing a sparse one, or adjusting preprocessing cascades through the entire model in ways that are hard to predict. Mitigations include:

- **Isolating feature groups** behind versioned feature stores so you can reason about what changed.
- **Sliced evaluation**: always evaluate model quality broken down by input cohort, not just in aggregate.
- **Regularisation audits**: periodically re-examine which features carry real signal versus noise correlation.

## Unstable Data Dependencies

ML systems are uniquely coupled to their data pipelines. Unlike code, data can change silently. Debt accumulates through:

- **Underspecified data contracts**: a consuming model assumes a feature's range is [0, 1] but an upstream transform changes without notice.
- **Feedback loops**: a model's predictions influence future training data (e.g. a recommendation system shapes user behaviour, which becomes the next training set).
- **Legacy pivots**: old feature engineering code kept "just in case" that no one dares delete.

Actionable practices:
- Define and enforce **data schemas** at pipeline boundaries using tools like Great Expectations, TFX Data Validation, or Pandera.
- Track **data provenance** — log the exact dataset version (hash or DVC tag) used for each training run.
- Instrument **distribution shift detectors** in production to catch when live data drifts from training data.

## Glue Code and Pipeline Jungles

Research code evolves into production glue: wrappers around open-source components, one-off scripts to move data between formats, notebooks-to-scripts ports done quickly. The result is a **pipeline jungle** — multiple parallel paths that all claim to produce the same thing, nobody sure which is canonical.

- Enforce a **single pipeline definition** per model, expressed in code (Airflow DAG, Metaflow flow, or a Makefile-style build). Delete the alternatives.
- Apply the **dead experimental code rule**: if an experiment branch hasn't been merged or deleted within one sprint, it gets deleted.

## Configuration Debt

ML jobs are often driven by large config blobs (learning rate, batch size, feature flags, data paths). Misconfigured runs waste GPU time and produce misleading results.

- All hyperparameters and data paths should be **versioned alongside code** (committed to the experiment tracking run, not just passed at runtime).
- Use **typed config objects** (Hydra, Pydantic settings) rather than raw YAML dicts so misconfiguration fails loudly at startup.
- Require **config diffs** in experiment PR descriptions: what exactly changed from the baseline run?

## Monitoring and Testing Debt

Production ML systems are often deployed with less monitoring than a CRUD web service.

- Beyond infrastructure metrics, monitor **model behavioural metrics**: prediction distribution, feature value distributions, business KPIs tied to model outputs.
- Write **invariant tests** for data pipelines: assert that no feature is all-null, that label distributions are within expected bounds before training starts.
- Automate **regression testing against a held-out golden dataset** on every merge — a 2% AUC drop should fail CI just as a broken unit test does.

## Source

- Sculley, D., Holt, G., Golovin, D., Davydov, E., Phillips, T., Ebner, D., Chaudhary, V., Young, M., Crespo, J.-F., & Dennison, D. (2015). **Hidden Technical Debt in Machine Learning Systems**. *Advances in Neural Information Processing Systems 28 (NeurIPS 2015)*. https://papers.nips.cc/paper/2015/hash/86df7dcfd896fcaf2674f757a2463eba-Abstract.html
- Google TFX team overview of ML pipeline best practices: https://www.tensorflow.org/tfx/guide
