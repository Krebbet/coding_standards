# Logging

Production-grade logging is a **product interface** — it is written for operators, on-call engineers, and future maintainers, not just for the developer. High-signal, low-volume, structured logs are the standard.

> **Note on DS vs SFL:** DS standards prescribed simple `logging.basicConfig` without format requirements. The resolved position (2026-04-16) is SFL supersedes: use structured, contextual logging. The DS rule of logging at entrypoints only is retained.

## Core Rules

### Use the Standard Library `logging` Module

Never use `print()` in scripts or library code. Use named loggers throughout.

```python
import logging
logger = logging.getLogger(__name__)
```

### Configure Only at Entrypoints

Library and module code **emits** logs via named loggers. Only the application entrypoint **configures** handlers, format, and levels (e.g., inside `if __name__ == "__main__"` or in an `app.py` startup function). Configuring in a module that will be imported will corrupt the caller's logging configuration.

### Use Levels Intentionally

| Level | When to Use |
|---|---|
| `DEBUG` | Diagnostic detail for developers — typically off in prod |
| `INFO` | Meaningful state changes and business events (start/stop, completed steps) |
| `WARNING` | Unexpected but recoverable conditions (fallbacks, retries) |
| `ERROR` | Operation failed; user impact likely |
| `CRITICAL` | Service integrity compromised; immediate action required |

### Structured, Contextual Logs

Include stable fields so logs are searchable and aggregatable:

- `event` — stable string naming what happened (e.g., `charge.started`)
- `component` — which module/service emitted the log
- `request_id` / `correlation_id` — trace a single request across modules
- `user_id` or `tenant_id` (if allowed by your data policy)
- `duration_ms` — for timed operations
- `status` — outcome
- `error_type` — on failure

Use `extra={}` with the stdlib logger to attach structured fields.

### Log Exceptions Once, at the Right Boundary

Log exceptions where you decide how to handle/surface the failure. Include the stack trace (`logger.exception(...)` does this automatically). Do not log the same exception at multiple layers.

### Never Log Secrets or PII

Establish "never log" rules: tokens, passwords, card numbers, raw personal data. Prefer allow-lists of safe fields. Treat logs as potentially widely accessible.

## Structuring a Logging Setup

```python
# Library code — emit, never configure
import logging
logger = logging.getLogger("payments")

def charge(req):
    logger.info("charge.started", extra={"event": "charge.started", "request_id": req.request_id})
    ...

# Entrypoint — configure once
import logging, json

class JsonFormatter(logging.Formatter):
    def format(self, record):
        payload = {"ts": self.formatTime(record), "level": record.levelname, "message": record.getMessage()}
        for key in ("event", "component", "request_id", "duration_ms"):
            if hasattr(record, key):
                payload[key] = getattr(record, key)
        return json.dumps(payload)

def configure_logging(level=logging.INFO):
    handler = logging.StreamHandler()
    handler.setFormatter(JsonFormatter())
    root = logging.getLogger()
    root.setLevel(level)
    root.handlers.clear()
    root.addHandler(handler)
```

## Common Anti-Patterns

- `print()` in production paths
- `logging.basicConfig()` inside an importable module
- Logging in tight loops without sampling
- Logging large payloads (log keys/IDs, not full objects)
- Duplicate exception logs at multiple layers
- Logging secrets, tokens, or PII

## Source
- `../raw/SFL Software Design Principles/Logging…md`
- `../raw/DS Code Development Manual/Writing Code/Code Standards/Code Standards Overview/Log Not Print…md`
- `../raw/DS Code Development Manual/Writing Code/Code Standards/Code Standards Overview/Logger Instantiation…md`
- `../raw/DS Code Development Manual/Writing Code/Code Standards/Code Standards Overview/Logging Configuration Location…md`

## Related
- [[principles/configuration-management]]
- [[code-style/python-standards]]
