# The Twelve-Factor App for ML Services

The Twelve-Factor App methodology (12factor.net) defines principles for building portable, scalable, maintainable services. All twelve factors apply to Python ML/DS services, but five are most commonly violated.

## The Five Factors Most Violated in ML Projects

### 1. Config — Store Config in the Environment

Configuration that varies between environments (DB URLs, API keys, model artifact paths, feature flag values, cloud bucket names) must come from environment variables, not be hardcoded in source or committed config files.

The test: can you open-source the codebase right now without exposing credentials? If not, you have config in code.

Use `pydantic-settings` — see [[principles/configuration-management]].

### 2. Processes — Stateless Services

ML serving processes must be stateless. Any persistent state (model weights, cached embeddings) should live in a backing service (S3, Redis, feature store), not in process memory that is lost on restart. Stateless services scale horizontally — run 10 replicas because none of them holds state the others need.

In-process caching is fine as a performance optimisation, but the cache must be rebuildable from an external store.

### 3. Dev/Prod Parity — Keep Environments As Similar As Possible

The gap between dev and prod is where bugs hide. Use the same:
- Python version (pin it in `.python-version`, `pyproject.toml`, and CI config)
- Library versions (same lockfile in dev, CI, and prod)
- Hardware architecture (M-series Mac vs x86 Linux can produce different model behaviour)
- Backing services (if PostgreSQL in prod, use PostgreSQL locally — not SQLite)

For ML specifically: if your feature store has a local stub for development, ensure it implements the same interface as prod.

### 4. Logs — Treat Logs as Event Streams

Never manage log files. Write all log output to stdout/stderr as a stream. The platform (Kubernetes, Cloud Run, ECS) routes this stream to your log aggregator.

Use structured JSON logging — see [[principles/logging]]. Include ML-specific fields: `model_version`, `prediction_latency_ms`, `request_id`.

### 5. Disposability — Fast Startup and Graceful Shutdown

ML serving containers should start in seconds. Slow cold starts (downloading a 2 GB model on every start) are a reliability failure. Options:
- Bake the model into the container image at build time
- Use an init container to pre-load the model before the serving process starts

On `SIGTERM`, finish in-flight requests then shut down cleanly — this enables the scheduler to reschedule pods without dropping requests.

## The Other Seven Factors (Summary for ML)

| Factor | ML Implication |
|---|---|
| Codebase | Single repo for training + serving code, not separate codebases |
| Dependencies | Pinned lockfile (`pyproject.toml` + `poetry.lock` or `requirements.txt`) — even patch versions matter for model behaviour |
| Backing services | DBs, S3 buckets, model registries, feature stores are attached resources configured via env vars |
| Build/Release/Run | Model training is a build concern; the model artefact belongs in a versioned store, not baked into every container rebuild |
| Port binding | FastAPI/Flask app binds to `0.0.0.0:$PORT` via uvicorn — no reliance on injected web server |
| Concurrency | Scale by running more instances (`web`, `worker`, `scheduler` process types independently) |
| Admin processes | Migrations, retraining triggers, backfills run as one-off jobs with the same codebase/config as the running app |

## Source
- https://12factor.net — Adam Wiggins (Heroku)

## Related
- [[principles/configuration-management]]
- [[principles/logging]]
- [[cloud/cicd-for-ml]]
- [[cloud/observability]]
- [[workflow/dependency-management]]
