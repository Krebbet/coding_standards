# Observability for ML Services

Observability is the ability to understand what is happening inside a system from its outputs. For ML services, it has two dimensions: **software observability** (is the service healthy?) and **model observability** (is the model still correct?).

## The Three Pillars

### 1. Structured Logging

Use structured JSON logging from the start — raw log lines are impossible to query at scale. See [[principles/logging]] for the general logging standard. For ML services, add these fields:

- `model_version` — which model version served this request
- `prediction_latency_ms` — end-to-end inference time
- `input_shape` or `input_hash` — what the model received (or a summary, if PII-sensitive)
- `prediction` / `confidence` — what the model returned
- `feature_store_latency_ms` — if a feature store was called

Use `structlog` for a clean structured logging setup in cloud services:

```python
import structlog
log = structlog.get_logger()

log.info(
    "prediction_made",
    model_version="v1.2.3",
    request_id=request_id,
    prediction=float(result),
    latency_ms=round((time.time() - start) * 1000, 2),
)
```

### 2. Metrics

Expose Prometheus-format metrics for cloud-native environments:

```python
from prometheus_client import Counter, Histogram, Gauge

REQUEST_COUNT = Counter("ml_requests_total", "Total inference requests", ["model_version", "status"])
INFERENCE_LATENCY = Histogram("ml_inference_duration_seconds", "Inference latency",
    buckets=[0.01, 0.05, 0.1, 0.25, 0.5, 1.0, 2.5])
MODEL_LOADED = Gauge("ml_model_loaded", "Whether the model is loaded", ["model_version"])
```

**Key metrics to expose for ML services:**

| Metric | Type | Why |
|---|---|---|
| `inference_requests_total` | Counter | Request rate and error rates |
| `inference_duration_seconds` | Histogram | p50/p95/p99 latency — SLA adherence |
| `prediction_confidence_score` | Histogram | Drift detection |
| `model_load_duration_seconds` | Gauge | Cold start performance |
| `feature_store_latency_seconds` | Histogram | Dependency health |
| `batch_rows_processed_total` | Counter | Batch job throughput |

### 3. Distributed Tracing

Use **OpenTelemetry** (CNCF standard) to trace requests across services. Propagate `traceparent` HTTP headers so a request through API Gateway → inference service → feature store appears as a single trace with child spans.

```python
from opentelemetry import trace
tracer = trace.get_tracer("ml-service")

with tracer.start_as_current_span("inference") as span:
    span.set_attribute("model.version", "v1.2.3")
    result = model.predict(features)
    span.set_attribute("prediction.class", str(result))
```

## Health Check Endpoints

Every service exposes `/health` (liveness) and `/ready` (readiness):

```python
@app.get("/health")
def health():
    return {"status": "ok"}

@app.get("/ready")
def ready():
    if model is None:
        raise HTTPException(503, detail="Model not loaded")
    return {"status": "ready", "model_version": MODEL_VERSION}
```

Kubernetes uses liveness probes to restart crashed pods, and readiness probes to stop routing traffic to pods that are not yet ready (e.g., still loading a large model).

## ML-Specific Monitoring

Standard infrastructure observability tells you the service is running. Model monitoring tells you whether the model is still correct.

**Data drift** — log prediction inputs periodically and compare their distribution against the training distribution. Tools: Evidently AI, WhyLogs, or custom PSI checks in a scheduled job. Alert when any feature's PSI exceeds 0.2.

**Prediction drift** — monitor the distribution of model outputs over time. A sudden shift in the fraction of positive predictions often indicates an upstream data pipeline issue.

**Ground truth monitoring** — when labels become available (e.g., customer churn eventually became observable), compute live accuracy/F1/RMSE and alert on degradation.

**Alerts to configure:**
- P95 inference latency > 500ms
- Error rate > 1% over 5 minutes
- Model not loaded (gauge = 0)
- Prediction confidence distribution shifts > 2 standard deviations from baseline
- Batch job failure or timeout

## Source
- OpenTelemetry: https://opentelemetry.io/docs/
- Prometheus best practices: https://prometheus.io/docs/practices/instrumentation/
- structlog: https://www.structlog.org/
- Evidently AI: https://evidentlyai.com/
- Google SRE Book: https://sre.google/sre-book/monitoring-distributed-systems/

## Related
- [[principles/logging]]
- [[cloud/cicd-for-ml]]
- [[cloud/mlops-maturity]]
- [[principles/ml-technical-debt]]
