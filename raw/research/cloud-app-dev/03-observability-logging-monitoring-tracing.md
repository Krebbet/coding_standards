# Observability for Cloud-Deployed Python ML/DS Services

Observability is the ability to understand what is happening inside a system from the outside by examining its outputs. For ML services, observability has two dimensions: standard software observability (is the service healthy?) and ML-specific observability (is the model performing well?).

## The Three Pillars of Observability

### 1. Structured Logging

Raw print statements and unstructured log lines are nearly impossible to query at scale. Use structured JSON logging from the start.

**Python setup with structlog:**

```python
import structlog
import logging
import sys

# Configure at app startup
structlog.configure(
    processors=[
        structlog.processors.TimeStamper(fmt="iso"),
        structlog.stdlib.add_log_level,
        structlog.processors.JSONRenderer(),
    ],
    wrapper_class=structlog.make_filtering_bound_logger(logging.INFO),
    logger_factory=structlog.PrintLoggerFactory(file=sys.stdout),
)

log = structlog.get_logger()

# In your inference handler:
log.info(
    "prediction_made",
    model_version="v1.2.3",
    request_id=request_id,
    input_shape=str(features.shape),
    prediction=float(result),
    latency_ms=round((time.time() - start) * 1000, 2),
)
```

Every log line is a JSON object that log aggregators (CloudWatch Logs Insights, Datadog, Loki) can query efficiently. Include `request_id` in all log lines within a request context (use `contextvars` or structlog's context binding to propagate it automatically).

**What to log:**
- Request start/end with latency
- Prediction inputs (or a hash/sample if PII-sensitive)
- Prediction outputs and confidence scores
- Model version used
- Feature store lookup latency
- Errors with full stack traces
- Slow queries or timeouts

**What not to log:**
- PII (names, emails, SSNs) in plaintext
- Full model weights or large tensors
- Passwords or tokens (use `SecretStr` to prevent accidental logging)

### 2. Metrics

Metrics are numeric measurements sampled over time. Use Prometheus-format metrics for cloud-native environments.

**Python setup with prometheus-client:**

```python
from prometheus_client import Counter, Histogram, Gauge, start_http_server

REQUEST_COUNT = Counter("ml_requests_total", "Total inference requests", ["model_version", "status"])
INFERENCE_LATENCY = Histogram(
    "ml_inference_duration_seconds",
    "Inference latency",
    buckets=[0.01, 0.05, 0.1, 0.25, 0.5, 1.0, 2.5, 5.0],
)
MODEL_LOADED = Gauge("ml_model_loaded", "Whether the model is loaded", ["model_version"])

# In handler:
with INFERENCE_LATENCY.time():
    result = model.predict(features)
REQUEST_COUNT.labels(model_version="v1.2.3", status="success").inc()
```

**Key metrics to expose for ML services:**
- `inference_requests_total` (counter, by status code and model version)
- `inference_duration_seconds` (histogram — p50, p95, p99 are critical)
- `model_load_duration_seconds` (gauge — at startup)
- `prediction_confidence_score` (histogram — for drift detection)
- `feature_store_latency_seconds` (histogram)
- `batch_job_rows_processed_total` (counter — for batch scoring jobs)

For Kubernetes, scrape metrics via ServiceMonitor (Prometheus Operator) and alert via AlertManager. For AWS, push custom metrics to CloudWatch Metrics using `boto3`.

### 3. Distributed Tracing

Tracing shows how a request flows through multiple services. Use OpenTelemetry — the CNCF standard that works with Jaeger, Zipkin, AWS X-Ray, and Datadog.

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter

provider = TracerProvider()
provider.add_span_processor(BatchSpanProcessor(OTLPSpanExporter()))
trace.set_tracer_provider(provider)

tracer = trace.get_tracer("ml-service")

with tracer.start_as_current_span("inference") as span:
    span.set_attribute("model.version", "v1.2.3")
    span.set_attribute("input.shape", str(features.shape))
    result = model.predict(features)
    span.set_attribute("prediction.class", str(result))
```

Propagate trace context through HTTP headers (`traceparent`) so that a request that goes from API Gateway -> inference service -> feature store shows as a single trace with child spans.

## ML-Specific Observability: Model Monitoring

Standard software observability tells you the service is running. Model monitoring tells you if the model is still correct.

**Data drift detection:** Log prediction inputs and periodically compare their distribution against the training distribution. Tools: Evidently AI, Whylogs, or custom KL-divergence checks in a scheduled job.

**Prediction drift:** Monitor the distribution of model outputs over time. A sudden shift in the fraction of positive predictions often indicates upstream data pipeline issues.

**Ground truth monitoring:** When labels become available (e.g., customer churn actually happened), compute live accuracy/F1/RMSE and alert when it drops below a threshold.

**Alerts to configure:**
- P95 inference latency > 500ms
- Error rate > 1% over 5 minutes
- Model not loaded (gauge = 0)
- Prediction confidence distribution shifts by > 2 standard deviations
- Batch job failure or timeout

## Health Check Endpoints

Every service should expose a `/health` (liveness) and `/ready` (readiness) endpoint:

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

Kubernetes uses liveness probes to restart crashed pods and readiness probes to stop routing traffic to pods that are not yet ready (e.g., still loading a large model).

## Source

- https://opentelemetry.io/docs/ — OpenTelemetry documentation (CNCF standard)
- https://prometheus.io/docs/practices/instrumentation/ — Prometheus instrumentation best practices
- https://www.structlog.org/en/stable/ — structlog documentation
- https://evidentlyai.com/blog/ml-monitoring-do-it-yourself — Evidently AI: ML monitoring guide
- https://sre.google/sre-book/monitoring-distributed-systems/ — Google SRE Book: Monitoring Distributed Systems
