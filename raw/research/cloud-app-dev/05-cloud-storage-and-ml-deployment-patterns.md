# Cloud Storage Patterns and ML Model Deployment

## Cloud Storage for Data and Models

### Object Storage as the Universal Backing Store

Cloud object storage (AWS S3, GCP Cloud Storage, Azure Blob Storage) is the foundation of virtually all cloud ML architectures. It is cheap, durable (11 nines), infinitely scalable, and accessible from any compute service. Every ML project should treat object storage as its primary artifact store.

**Recommended directory structure in S3/GCS:**

```
s3://my-project-bucket/
  data/
    raw/                         # immutable source data
      2024-01-15/events.parquet
    processed/                   # feature-engineered datasets
      v3/train.parquet
      v3/test.parquet
  models/
    experiments/                 # candidate models from training runs
      run-abc123/model.pkl
      run-abc123/metrics.json
    registry/                    # promoted, named model versions
      churn-model/v1.2.3/model.pkl
      churn-model/v1.2.3/metadata.json
  artifacts/
    reports/
    evaluation/
```

### Accessing S3 from Python

Use `boto3` or the higher-level `s3fs` / `fsspec` for DataFrame workflows:

```python
import boto3
import pandas as pd
import s3fs

# Direct boto3 download
s3 = boto3.client("s3")
s3.download_file("my-bucket", "models/churn-model/v1.2.3/model.pkl", "/tmp/model.pkl")

# fsspec — works with pandas, pyarrow, dask transparently
df = pd.read_parquet("s3://my-bucket/data/processed/v3/train.parquet")

# Write back to S3
df.to_parquet("s3://my-bucket/data/processed/v4/train.parquet", index=False)
```

For large datasets, use `pyarrow` with S3 directly to avoid materializing entire datasets in memory:

```python
import pyarrow.parquet as pq
import pyarrow.dataset as ds

dataset = ds.dataset("s3://my-bucket/data/processed/v3/", format="parquet")
# Lazy scan — only reads data you actually query
table = dataset.to_table(filter=ds.field("date") > "2024-01-01")
```

### IAM and Storage Security

Never use long-lived access keys for S3 access in cloud compute. Instead:
- Assign an **IAM role** to EC2 instances, ECS tasks, or Lambda functions — credentials are provided automatically via the instance metadata service
- For Kubernetes, use **IAM Roles for Service Accounts (IRSA)** on EKS, or Workload Identity on GKE
- For local development, use AWS SSO profiles or `aws configure sso`

Bucket policies should enforce:
- Server-side encryption at rest (SSE-S3 or SSE-KMS)
- No public access (block all public access settings enabled)
- Object versioning for critical buckets (model registry, processed data)
- Lifecycle policies to transition old artifacts to cheaper storage tiers (S3 Glacier after 90 days)

---

## ML Model Deployment Patterns

### Pattern 1: Synchronous REST API (Real-Time Inference)

The standard pattern for user-facing predictions where latency matters (< 500ms).

**Technology stack:**
- FastAPI (preferred for Python) + uvicorn + gunicorn
- Containerized and deployed to Kubernetes, AWS ECS, or Google Cloud Run
- Load balancer (ALB, Cloud Load Balancing) in front

```python
from fastapi import FastAPI
from pydantic import BaseModel
import joblib

app = FastAPI()
model = joblib.load("model_artifacts/model.pkl")  # loaded once at startup

class PredictRequest(BaseModel):
    feature_1: float
    feature_2: int
    feature_3: str

class PredictResponse(BaseModel):
    prediction: int
    probability: float
    model_version: str

@app.post("/predict", response_model=PredictResponse)
def predict(request: PredictRequest):
    features = preprocess(request)
    prob = model.predict_proba([features])[0][1]
    return PredictResponse(
        prediction=int(prob > 0.5),
        probability=float(prob),
        model_version="v1.2.3",
    )
```

**Scaling considerations:**
- Stateless pods; scale horizontally via HPA (CPU or custom metrics)
- Load model once per process, not per request
- Use connection pooling for any backing database lookups
- Consider GPU sharing for deep learning models (NVIDIA MPS or time-slicing)

### Pattern 2: Asynchronous Inference Queue

For workloads where latency tolerance is higher (seconds to minutes) or compute is expensive.

Architecture: `Client -> API -> Queue (SQS/Pub/Sub) -> Worker Pods -> Results Store`

1. Client POSTs a request to the API, receives a `job_id` immediately
2. API enqueues the job to SQS/Pub/Sub/Redis
3. Worker pods dequeue jobs, run inference, write results to a database or S3
4. Client polls `GET /results/{job_id}` or receives a webhook callback

This pattern is ideal for: batch inference on large inputs, expensive models (LLMs, diffusion models), video/audio processing.

### Pattern 3: Batch Scoring Jobs

For scheduled predictions on an entire dataset (e.g., nightly churn scores for all customers).

```python
# batch_score.py — runs as a Kubernetes CronJob or AWS Batch job
import pandas as pd
import boto3
import joblib

def run_batch_scoring(input_path: str, output_path: str, model_path: str):
    model = joblib.load(model_path)
    df = pd.read_parquet(input_path)
    df["prediction"] = model.predict(df[FEATURE_COLUMNS])
    df["probability"] = model.predict_proba(df[FEATURE_COLUMNS])[:, 1]
    df["scored_at"] = pd.Timestamp.utcnow()
    df[OUTPUT_COLUMNS].to_parquet(output_path, index=False)

if __name__ == "__main__":
    run_batch_scoring(
        input_path=os.environ["INPUT_PATH"],
        output_path=os.environ["OUTPUT_PATH"],
        model_path=os.environ["MODEL_PATH"],
    )
```

**Kubernetes CronJob definition:**
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: nightly-churn-scoring
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: scorer
            image: myapp/scorer:v1.2.3
            env:
            - name: INPUT_PATH
              value: "s3://my-bucket/data/processed/latest/customers.parquet"
            - name: OUTPUT_PATH
              value: "s3://my-bucket/predictions/churn/"
            - name: MODEL_PATH
              value: "s3://my-bucket/models/registry/churn-model/v1.2.3/model.pkl"
          restartPolicy: OnFailure
```

### Pattern 4: Serverless Inference (Lambda / Cloud Functions)

For infrequent predictions with unpredictable traffic where you want zero-idle cost.

- Package model + code into a Lambda layer or container image (up to 10GB image)
- Cold start penalty: 1–5 seconds for large models — mitigate with provisioned concurrency
- Best for: lightweight models (< 500MB), infrequent requests, event-driven architectures
- Not suitable for: GPU models, models requiring > 15 minutes execution, streaming responses

### Choosing the Right Pattern

| Pattern | Latency | Throughput | Cost Model | Best For |
|---|---|---|---|---|
| REST API | Low (< 500ms) | Medium | Per-instance-hour | User-facing, real-time |
| Async Queue | Medium (1s–5min) | High | Per-instance-hour | Expensive models, large inputs |
| Batch Job | High (minutes–hours) | Very High | Per-job-hour | Scheduled scoring, analytics |
| Serverless | Variable | Low–Medium | Per-invocation | Infrequent, event-triggered |

## Source

- https://docs.aws.amazon.com/wellarchitected/latest/machine-learning-lens/wellarchitected-machine-learning-lens.html — AWS Well-Architected ML Lens
- https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning — Google Cloud MLOps architecture
- https://docs.aws.amazon.com/AmazonS3/latest/userguide/best-practices.html — AWS S3 best practices
- https://fastapi.tiangolo.com/deployment/concepts/ — FastAPI deployment concepts
- https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/ — Kubernetes CronJobs
