# Reproducibility in ML: Versioning Data, Code, and Models

Reproducibility is a first-class engineering concern, not an academic nicety. The inability to reproduce a result means you cannot safely iterate on it, debug it, or hand it off. Full reproducibility requires versioning three distinct artefacts: code, data, and the environment.

## The Three-Version Rule

A training run is only reproducible if you can recover the exact state of all three:

| Artefact | Tool | What to store |
|---|---|---|
| Code | Git | commit SHA, not a branch name |
| Data | DVC / LakeFS / Delta Lake | dataset hash or version tag |
| Environment | Docker / conda lock file | image digest or resolved lock file |

Log all three identifiers to your experiment tracker at run start. MLflow's `mlflow.log_param` and Weights & Biases `wandb.config` both support this pattern.

## Data Versioning in Practice

Raw data should be treated like source code: never mutated in place, always appended to or replaced by a new version. Practical approaches:

- **DVC (Data Version Control)**: stores large files in a content-addressable cache (S3, GCS, Azure), with a `.dvc` pointer file committed to git. `dvc repro` re-executes the full pipeline from any historical commit.
- **Delta Lake / Iceberg**: for data warehouse tables, use time-travel queries (`VERSION AS OF`) so a training job can pin to a table snapshot by timestamp or version number.
- **Feature stores** (Feast, Tecton, Hopsworks): separate the feature computation from its consumption, with point-in-time correct retrieval that prevents training-serving skew.

Never train on a mutable table without first materialising a snapshot with a recorded identifier.

## Experiment Tracking Standards

Experiment tracking tools (MLflow, Weights & Biases, Neptune, Comet) are only useful if the team agrees on what to log. Adopt a standard schema:

- **Params**: every hyperparameter, data version, feature set name, random seed.
- **Metrics**: all evaluation metrics (train, val, test splits separately), computed at each epoch and at run end.
- **Artefacts**: the trained model file, preprocessing pipeline, feature importance plots.
- **Tags**: run type (`baseline`, `experiment`, `production`), author, ticket/issue reference.

Enforce this via a shared experiment logging wrapper, not by relying on individuals to remember. The wrapper should also log git commit SHA and diff status (dirty vs clean) automatically.

## Seeding and Determinism

Even with identical data and code, results can differ due to non-determinism. Mitigation:

- Set **all random seeds**: Python `random`, `numpy`, framework-specific (`torch.manual_seed`, `tf.random.set_seed`), and environment variable `PYTHONHASHSEED`.
- For GPU training, set `torch.backends.cudnn.deterministic = True` (with accepted performance cost) during baseline runs.
- Document which operations are **known non-deterministic** (e.g. some CUDA scatter ops) and accept that exact numerical equality may not be achievable at scale — target result stability within a confidence interval instead.

## Model Registry as the Source of Truth

A trained model artefact should only enter production via a **model registry** with a promotion workflow:

1. Train → log to experiment tracker with status `candidate`.
2. Evaluate against offline test set and any required fairness/bias checks → status `validated`.
3. Shadow deployment or A/B test → status `staging`.
4. Full rollout → status `production`.

MLflow Model Registry and W&B Artifacts both support this state machine. The key constraint: no model reaches production without a registered run that captures what data, code, and config produced it.

## Source

- DVC documentation — Data and Model Versioning: https://dvc.org/doc/use-cases/versioning-data-and-models
- MLflow documentation — Tracking Experiments: https://mlflow.org/docs/latest/tracking.html
- Huyen, C. (2022). *Designing Machine Learning Systems*. O'Reilly Media. (Chapter 6: Model Development and Offline Evaluation)
- Weights & Biases — A Guide to Experiment Tracking: https://docs.wandb.ai/guides/track
- Delta Lake time travel documentation: https://docs.delta.io/latest/delta-batch.html#-deltatimetravel
