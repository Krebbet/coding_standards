# The Twelve-Factor App Applied to Python ML/DS Services

The Twelve-Factor App methodology (https://12factor.net) provides a set of principles for building software-as-a-service applications that are portable, scalable, and maintainable. While originally written for web services, each factor has direct and important implications for Python-based ML and data science applications deployed to the cloud.

## The Twelve Factors and Their ML/DS Implications

### 1. Codebase — One codebase tracked in version control, many deploys

Every ML project should live in a single Git repository. Avoid the anti-pattern of separate repos for "training code" and "serving code" — these should be one codebase with different entry points (e.g., `train.py` vs. `serve.py`). Multiple environments (dev, staging, prod) are deployed from the same repo at different commits, not from different codebases.

### 2. Dependencies — Explicitly declare and isolate dependencies

Use `pyproject.toml` (with `uv` or `poetry`) or `requirements.txt` with pinned versions. Never rely on system-installed packages. For ML projects, pin exact versions of `numpy`, `pandas`, `scikit-learn`, `torch`, etc. since even patch-level version differences can alter model behavior. Use virtual environments or containers to isolate dependencies completely — never assume a system Python has what you need.

### 3. Config — Store config in the environment

Configuration that varies between deploys — database URLs, API keys, model artifact paths, feature flag values, cloud storage bucket names — must be read from environment variables, not hardcoded in source or committed config files. In Python, use `os.environ.get("MODEL_BUCKET", "default-bucket")` or a library like `pydantic-settings`. The test for compliance: can you open-source the codebase right now without exposing credentials? If not, you have config in code.

### 4. Backing Services — Treat backing services as attached resources

Databases, S3/GCS buckets, Redis caches, feature stores, model registries (MLflow, Vertex AI Model Registry) — all are attached resources referenced by URL/credentials from config. The application should be able to swap a local PostgreSQL for an RDS instance by changing an env var, with no code changes. This is especially important for ML services that often depend on multiple external stores (feature stores, artifact stores, experiment trackers).

### 5. Build, Release, Run — Strictly separate build and run stages

The **build** stage compiles code and packages dependencies (e.g., `docker build`). The **release** stage combines the build artifact with environment-specific config. The **run** stage executes in the target environment. For ML: model training is a build-time concern; model artifacts should be stored in a versioned artifact store, not baked into the container. The container image should be the same binary that goes from staging to production.

### 6. Processes — Execute the app as one or more stateless processes

ML serving processes should be stateless. Any state (model weights, cached embeddings, session data) should live in a backing service, not in process memory that gets lost on restart. This enables horizontal scaling — you can run 10 replicas of your FastAPI model server because none of them holds state that other replicas need. In-process caching is acceptable as a performance optimization, but the cache must be rebuildable from an external store.

### 7. Port Binding — Export services via port binding

Your ML service should be a self-contained process that binds to a port and serves requests. Use `uvicorn` or `gunicorn` to bind your FastAPI/Flask app to `0.0.0.0:$PORT`. Do not rely on an external web server (nginx, Apache) being injected at runtime — the app itself is the web server. This makes the service trivially runnable locally and in any container runtime.

### 8. Concurrency — Scale out via the process model

Scale horizontally by running more instances, not vertically by running bigger machines (where avoidable). Design ML workloads as discrete process types: a `web` process type for real-time inference, a `worker` process type for async inference jobs, a `scheduler` process type for batch scoring runs. Each scales independently.

### 9. Disposability — Maximize robustness with fast startup and graceful shutdown

ML serving containers should start in seconds, not minutes. Lazy-load models only if necessary; better yet, load them at startup and keep them warm. On `SIGTERM`, finish in-flight requests, then shut down cleanly. This enables the cloud scheduler to reschedule pods during deployments and autoscaling events without dropping requests. Slow startup (e.g., downloading a 2 GB model on every cold start) is a disposability failure — bake the model into the image or use init containers to pre-load it.

### 10. Dev/Prod Parity — Keep development, staging, and production as similar as possible

The gap between dev and prod is where bugs hide. Use Docker Compose locally to mirror your cloud services. If you use PostgreSQL in prod, use PostgreSQL locally (not SQLite). If you use a feature store in prod, stub it locally with the same interface. ML-specific: use the same Python version, same library versions, same hardware architecture (avoid training on Apple Silicon M-series if deploying to x86 Linux servers without testing the portability).

### 11. Logs — Treat logs as event streams

Applications should never manage log files. Write all log output to stdout/stderr as a stream of events. The cloud platform (Kubernetes, Cloud Run, ECS) captures and routes this stream to your log aggregator (CloudWatch, Cloud Logging, Datadog). In Python: configure the `logging` module to write to stdout, use structured JSON logging (e.g., `structlog` or `python-json-logger`) so logs are machine-parseable. Include contextual fields: `model_version`, `request_id`, `prediction_latency_ms`.

### 12. Admin Processes — Run admin/management tasks as one-off processes

Database migrations, model retraining triggers, data backfill jobs — these should be one-off processes run against a release using the same codebase and config as that release. Use Kubernetes Jobs or cloud run jobs for these, not SSH sessions into running containers. Example: `kubectl run migrate --image=myapp:v1.2.3 --command -- python manage.py migrate`.

## Summary for ML Teams

The factors most commonly violated in ML projects are: **Config** (credentials in notebooks), **Processes** (stateful servers), **Dev/Prod Parity** (training locally on different library versions), **Logs** (writing logs to files), and **Disposability** (slow cold starts due to large model loading). Addressing these five factors first yields the largest reliability and operability gains.

## Source

- https://12factor.net — The original methodology by Adam Wiggins (Heroku)
- https://www.12factor.net/config
- https://www.12factor.net/logs
