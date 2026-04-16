# Reproducibility in ML

Reproducibility is a first-class engineering concern. The inability to reproduce a result means you cannot safely iterate on it, debug it, or hand it off. Full reproducibility requires versioning three artefacts: code, data, and the environment.

## The Three-Version Rule

A training run is only reproducible if you can recover the exact state of all three:

| Artefact | Tool | What to Store |
|---|---|---|
| Code | Git | Commit SHA (not a branch name) |
| Data | DVC / Delta Lake / LakeFS | Dataset hash or version tag |
| Environment | Docker / lockfile | Image digest or resolved lockfile |

Log all three identifiers to your experiment tracker at the start of every run. MLflow's `mlflow.log_param` and W&B's `wandb.config` both support this.

## Data Versioning

Raw data should be treated like source code: never mutated in place, always replaced by a new version.

- **DVC** — stores large files in a content-addressable cache (S3, GCS, Azure) with a `.dvc` pointer file committed to git. `dvc repro` re-executes the full pipeline from any historical commit
- **Delta Lake / Iceberg** — for warehouse tables, use time-travel queries to pin a training job to a table snapshot by timestamp or version number
- **Feature stores** (Feast, Tecton) — separate feature computation from feature retrieval, with point-in-time correct retrieval that prevents training-serving skew

Never train on a mutable table without first materialising a snapshot with a recorded identifier.

## Experiment Tracking Standards

Experiment trackers (MLflow, Weights & Biases, Neptune) are only useful if the team agrees on what to log. Adopt a standard schema:

| Category | What to Log |
|---|---|
| **Params** | All hyperparameters, data version, feature set name, random seed |
| **Metrics** | All evaluation metrics (train/val/test separately), per epoch and at run end |
| **Artefacts** | Trained model file, preprocessing pipeline, feature importance plots |
| **Tags** | Run type (`baseline`, `experiment`, `production`), author, ticket reference |

Enforce this via a shared experiment logging wrapper, not by relying on individuals to remember. The wrapper should automatically log the git commit SHA and whether the repo was clean or dirty.

## Seeding and Determinism

Even with identical data and code, results can differ due to non-determinism. Steps to mitigate:

```python
import random, os
import numpy as np
import torch

SEED = 42

random.seed(SEED)
os.environ["PYTHONHASHSEED"] = str(SEED)
np.random.default_rng(SEED)  # modern API — pass rng as parameter
torch.manual_seed(SEED)
torch.backends.cudnn.deterministic = True  # accepted performance cost
```

Document which operations are **known non-deterministic** (some CUDA scatter ops, for example) and accept that exact numerical equality may not be achievable at scale — target result stability within a confidence interval instead.

## Model Registry as the Source of Truth

No model should reach production without a registered, traceable run. The promotion workflow:

1. Train → log to experiment tracker with status `candidate`
2. Evaluate against offline test set and any fairness/bias checks → status `validated`
3. Shadow deployment or A/B test → status `staging`
4. Full rollout → status `production`

MLflow Model Registry and W&B Artifacts both support this state machine. The key constraint: no model reaches production without a run that records what data, code, and config produced it.

## Source
- DVC: https://dvc.org/doc/use-cases/versioning-data-and-models
- MLflow Tracking: https://mlflow.org/docs/latest/tracking.html
- Huyen, C. (2022). *Designing Machine Learning Systems*. O'Reilly. Chapter 6.

## Related
- [[principles/ml-technical-debt]]
- [[principles/data-pipeline-design]]
- [[cloud/mlops-maturity]]
- [[cloud/cicd-for-ml]]
- [[workflow/dependency-management]]
