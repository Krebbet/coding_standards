# CI/CD Pipelines for ML/DS Projects

Continuous Integration and Continuous Delivery for machine learning (CD4ML) extends traditional CI/CD to handle the unique challenges of ML: data dependencies, non-deterministic training, large artifacts, and the need to validate model quality — not just code correctness.

## Why Standard CI/CD Is Not Enough for ML

In traditional software, a passing test suite means the code is correct. In ML, correct code can still produce a bad model. CI/CD for ML must validate three things:
1. **Code correctness** — unit tests, integration tests (as in traditional CI)
2. **Data quality** — the training data meets expected schema and statistical properties
3. **Model quality** — the trained model meets performance thresholds before it is promoted

## The ML CI/CD Pipeline Stages

### Stage 1: Code Validation (CI)

Triggered on every pull request and push:

```
lint (ruff/flake8) -> type check (mypy) -> unit tests (pytest) -> integration tests
```

Unit tests for ML code should test:
- Feature engineering functions (with known inputs and expected outputs)
- Data preprocessing transforms (test for correct shapes, types, no NaNs)
- Model loading and inference with a dummy model artifact
- API endpoint contracts (request/response schema validation)

Integration tests should spin up lightweight versions of dependencies (e.g., localstack for S3, a test database) and test the full pipeline on a small sample dataset.

### Stage 2: Data Validation

Before training, validate that the input data meets expectations. Use Great Expectations or Pandera:

```python
import pandera as pa
from pandera import Column, DataFrameSchema

schema = DataFrameSchema({
    "feature_1": Column(float, nullable=False),
    "feature_2": Column(int, pa.Check.ge(0)),
    "label": Column(int, pa.Check.isin([0, 1])),
})

schema.validate(training_df)  # raises SchemaError if data is invalid
```

Fail the pipeline if data validation fails — never train on corrupt data.

### Stage 3: Model Training

For reproducibility:
- Pin random seeds (`numpy.random.seed`, `torch.manual_seed`)
- Log all hyperparameters to an experiment tracker (MLflow, Weights & Biases)
- Store the training dataset version (hash or DVC pointer)
- Store the trained model artifact in a versioned model registry

Training should be a reproducible, parameterized job:

```bash
python train.py \
  --data-version sha256:abc123 \
  --experiment-name "v1.2-feature-experiment" \
  --output-bucket s3://my-models/experiments/
```

### Stage 4: Model Evaluation (Model Validation Gate)

This is the critical ML-specific gate. Compute evaluation metrics on a held-out test set and compare against a threshold and/or the current production model:

```python
metrics = evaluate_model(model, test_dataset)

THRESHOLDS = {"accuracy": 0.85, "f1": 0.80, "auc": 0.88}

for metric, threshold in THRESHOLDS.items():
    if metrics[metric] < threshold:
        raise ValueError(f"Model failed gate: {metric}={metrics[metric]:.3f} < {threshold}")

# Also compare against current production model
prod_metrics = get_prod_model_metrics()
if metrics["auc"] < prod_metrics["auc"] - 0.01:
    raise ValueError("New model is worse than production model")
```

Only models that pass this gate are promoted to the model registry as release candidates.

### Stage 5: Model Registration and Packaging

Package the validated model for deployment:
- Register in MLflow Model Registry with `Staging` status
- Build a Docker image that includes the model artifact (or mounts it from S3)
- Tag the Docker image with the model version and git SHA
- Push to a container registry (ECR, GCR, GHCR)

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY src/ ./src/
# Model artifact baked in (for fast startup), or use init container
COPY model_artifacts/ ./model_artifacts/
CMD ["uvicorn", "src.serve:app", "--host", "0.0.0.0", "--port", "8080"]
```

### Stage 6: Deployment

Use progressive delivery to minimize blast radius:

1. **Canary deployment**: Route 5% of traffic to the new model, monitor error rates and latency for 30 minutes
2. **Shadow deployment**: Run the new model in parallel without serving its predictions — compare outputs against production
3. **Blue-green deployment**: Deploy the new model alongside the old one; switch traffic atomically; keep old version warm for fast rollback

For Kubernetes, use Argo Rollouts or Flagger for automated canary analysis. For AWS SageMaker, use multi-variant endpoints.

### Stage 7: Post-Deployment Monitoring and Rollback

After promotion to production:
- Monitor the metrics defined in the observability doc (latency, error rate, prediction drift)
- Set automated rollback triggers: if error rate > 2% over 10 minutes, roll back to the previous model version automatically
- Keep the previous model artifact and deployment config for at least 30 days

## Pipeline Infrastructure Options

| Platform | Strengths | Best For |
|---|---|---|
| GitHub Actions | Simple, integrated with GitHub, free for public repos | Most teams starting out |
| GitLab CI | Strong artifact management, built-in container registry | GitLab-hosted repos |
| Kubeflow Pipelines | Native Kubernetes, ML-focused DAGs | Large-scale Kubernetes ML teams |
| AWS SageMaker Pipelines | Tight AWS integration, managed compute | AWS-native ML platforms |
| Prefect / Dagster | Python-native, good for data pipelines + ML | Data engineering + ML teams |
| MLflow Recipes | Standardized ML pipelines | Structured ML projects using MLflow |

## Artifact Versioning with DVC

For large datasets and model artifacts that cannot live in git, use DVC (Data Version Control):

```bash
dvc add data/training_set.parquet  # creates data/training_set.parquet.dvc
git add data/training_set.parquet.dvc  # commit the pointer
dvc push  # push actual data to S3/GCS
```

This gives you git-tracked pointers to exact dataset versions, enabling full reproducibility without storing binary blobs in git.

## Source

- https://martinfowler.com/articles/cd4ml.html — Continuous Delivery for Machine Learning (Fowler/Thoughtworks)
- https://ml-ops.org/content/mlops-principles — MLOps principles
- https://dvc.org/doc/use-cases/versioning-data-and-models — DVC data versioning
- https://mlflow.org/docs/latest/model-registry.html — MLflow Model Registry
- https://argoproj.github.io/argo-rollouts/ — Argo Rollouts progressive delivery
