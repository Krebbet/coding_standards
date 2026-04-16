# CI/CD for ML Projects (CD4ML)

Continuous Delivery for Machine Learning (CD4ML) extends traditional CI/CD to handle ML's unique challenges: data dependencies, non-deterministic training, large artifacts, and the need to validate model quality — not just code correctness.

## Why Standard CI/CD Is Not Enough

In traditional software, a passing test suite means the code is correct. In ML, correct code can still produce a bad model. A complete ML CI/CD pipeline must validate:

1. **Code correctness** — unit tests, integration tests (standard CI)
2. **Data quality** — training data meets expected schema and statistical properties
3. **Model quality** — trained model meets performance thresholds before promotion

## The 7-Stage Pipeline

### Stage 1: Code Validation
Triggered on every PR and push:
```
lint (ruff) → type check (mypy) → unit tests (pytest) → integration smoke test
```

Unit tests should cover feature transforms, preprocessing, model loading/inference with a dummy artifact, and API schema contracts. See [[testing/ml-testing-strategies]].

### Stage 2: Data Validation
Before training, validate input data against a schema. **Fail the pipeline if data validation fails** — never train on corrupt data.

```python
import pandera as pa
schema = pa.DataFrameSchema({
    "feature_1": pa.Column(float, nullable=False),
    "label": pa.Column(int, pa.Check.isin([0, 1])),
})
schema.validate(training_df)
```

### Stage 3: Model Training
For reproducibility, every training run must:
- Pin all random seeds
- Log all hyperparameters to an experiment tracker (MLflow, W&B)
- Record the training dataset version (DVC hash or Delta Lake snapshot)
- Store the trained model artifact in a versioned model registry

### Stage 4: Model Evaluation Gate
This is the critical ML-specific gate. Compute metrics on a held-out test set and compare against thresholds and the current production model:

```python
THRESHOLDS = {"accuracy": 0.85, "f1": 0.80, "auc": 0.88}

for metric, threshold in THRESHOLDS.items():
    if metrics[metric] < threshold:
        raise ValueError(f"Model failed gate: {metric}={metrics[metric]:.3f} < {threshold}")

# Also check: new model must not regress vs production
if metrics["auc"] < prod_metrics["auc"] - 0.01:
    raise ValueError("New model is worse than production model")
```

Only models that pass this gate are promoted to the model registry as release candidates.

### Stage 5: Model Registration and Packaging
- Register in model registry (MLflow, SageMaker Model Registry) with `Staging` status
- Build a Docker image tagged with model version and git SHA
- Push to a container registry (ECR, GCR, GHCR)

### Stage 6: Progressive Deployment
Use progressive delivery to minimise blast radius:
- **Shadow** — run the new model in parallel without serving its predictions; compare outputs to production
- **Canary** — route 5–10% of traffic to the new model; monitor for 30 minutes before full rollout
- **Blue-green** — deploy the new model alongside the old; switch traffic atomically; keep old version warm for fast rollback

### Stage 7: Post-Deployment Monitoring and Rollback
- Monitor error rate, latency, prediction distribution, and feature drift — see [[cloud/observability]]
- Set automated rollback triggers: if error rate > 2% over 10 minutes, roll back to the previous model version
- Retain the previous model artifact and deployment config for at least 30 days

## Artifact Versioning with DVC

For large datasets and model artifacts that cannot live in git:

```bash
dvc add data/training_set.parquet   # creates .dvc pointer file
git add data/training_set.parquet.dvc
dvc push                             # push data to S3/GCS
```

This gives git-tracked pointers to exact dataset versions, enabling full reproducibility without binary blobs in git.

## Source
- Fowler / Thoughtworks — Continuous Delivery for Machine Learning: https://martinfowler.com/articles/cd4ml.html
- DVC: https://dvc.org/doc/
- MLflow Model Registry: https://mlflow.org/docs/latest/model-registry.html

## Related
- [[testing/ml-testing-strategies]]
- [[principles/reproducibility]]
- [[cloud/observability]]
- [[cloud/mlops-maturity]]
- [[workflow/dependency-management]]
