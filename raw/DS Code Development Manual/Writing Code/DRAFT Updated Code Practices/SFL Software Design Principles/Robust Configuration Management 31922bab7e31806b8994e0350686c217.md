# Robust Configuration Management

### Goal

Make runtime configuration **explicit, typed, validated, and environment-specific**—without leaking secrets or scattering `os.getenv()` calls across the codebase. We use `pydantic-settings` to load environment variables into a single `Settings` object that the app consumes.

### Principles

- **Single source of truth:** One `Settings` model per service/module boundary; no ad-hoc env reads in business logic.
- **Typed configuration:** Every config value has an explicit type (e.g., `int` ports, `AnyUrl`/URL types, `bool` flags, enums for modes).
- **Fail fast:** Missing/invalid required config should stop startup with a clear error message.
- **Secure by default:** Secrets come from env/secret managers; never committed in repo, never logged.
- **Predictable precedence:** Define and document the order (typically: explicit env vars override `.env`, which overrides defaults).

### Standard pattern

- Create a `Settings` model that:
    - Declares all config fields (required vs optional) with human-readable descriptions.
    - Sets an **env var prefix** (e.g., `APP_`) to avoid collisions.
    - Optionally supports a local `.env` for developer convenience (not for production).
    - Controls parsing behavior (e.g., case sensitivity, handling of extra/unknown vars).
- Instantiate settings once at startup and pass it in (dependency injection), rather than using global mutable config.

### What goes in env vars (and what doesn’t)

**Use env vars for:**

- Deployment-specific values: environment name, service URLs, ports, timeouts, feature flags.
- Credentials/secrets: API keys, DB passwords (ideally injected from a secrets manager into env at runtime).
- Operational tuning: log level, tracing/export endpoints, pool sizes.

**Do not use env vars for:**

- Large structured config blobs that belong in code/config files with change control.
- Per-request/user settings.
- Values that should be consistent across builds (those belong in code, build metadata, or versioned config).

### Validation & ergonomics

- **Required fields:** If the app cannot run without it (DB URL, auth issuer, etc.), make it required and let startup fail.
- **Defaults:** Only for values that are safe and truly environment-agnostic (e.g., `LOG_FORMAT=json` might be fine; `DEBUG=true` generally not).
- **Cross-field rules:** Enforce invariants (e.g., if `AUTH_ENABLED` then `AUTH_ISSUER` must be set; timeouts must be positive; mutually exclusive modes).
- **Helpful errors:** Prefer clear field names and descriptions so startup errors are actionable.

### Naming conventions

- Use uppercase snake case env var names: `APP_DATABASE_URL`, `APP_LOG_LEVEL`.
- Use a consistent prefix per service: `BILLING_…`, `SEARCH_…`, etc.
- Prefer positive booleans: `APP_AUTH_ENABLED` (avoid double-negatives like `DISABLE_AUTH`).

### Secrets handling

- Treat secret fields as sensitive:
    - Never print settings objects directly.
    - Redact known secret fields in logs and error reports.
- Production should source secrets from an approved secret store; env vars are the transport mechanism, not the system of record.

### Local development & CI/CD

- Local dev may use `.env` for convenience, but:
    - Keep `.env` out of git (use `.env.example` with placeholders).
    - Ensure CI does not depend on developer `.env` files.
- In containers/Kubernetes, configure via deployment manifests/secret injection so config is fully externalized.

### Testing guidance

- Unit tests should construct settings with explicit values (or controlled env) to avoid coupling to developer machines.
- Add a “config contract” test that ensures:
    - Required variables are documented.
    - Unknown env vars are either rejected or intentionally tolerated (choose one policy and be consistent).

### Poor Config Management Example

```python
"""
BAD configuration management example (anti-patterns):
- reads env vars scattered across code
- insecure defaults (debug on; weak secret)
- no validation / type coercion issues
- logs secrets
- global mutable config
"""

import os
import logging

logger = logging.getLogger(__name__)

# Global mutable configuration (hard to test; can be changed at runtime)
CONFIG = {}

def init_config():
    # Scattered os.getenv calls with inconsistent naming and defaults
    CONFIG["debug"] = os.getenv("DEBUG", "true")  # string, not bool; insecure default
    CONFIG["port"] = os.getenv("PORT", "8000")    # string, not int
    CONFIG["db_url"] = os.getenv("DATABASE_URL")  # may be None; not checked
    CONFIG["api_key"] = os.getenv("API_KEY", "dev-api-key")  # secret default in code

    # Secret leakage in logs
    logger.info("Loaded config: %s", CONFIG)

def connect_to_db():
    # Reads env/config deep in business logic
    db_url = os.getenv("DATABASE_URL") or CONFIG.get("db_url")

    # Proceeds without validation; fails later and harder
    if not db_url:
        logger.warning("DATABASE_URL missing; continuing anyway...")

    # Type bug: comparing strings to ints later, or passing wrong types into libraries
    port = CONFIG.get("port")  # "8000"
    logger.info("Connecting to %s on port %s", db_url, port)

def handler():
    # Feature flags as strings, truthiness bugs ("false" is truthy)
    if CONFIG.get("debug"):
        logger.debug("Debug mode enabled")  # may always run due to string truthiness

if __name__ == "__main__":
    logging.basicConfig(level=logging.INFO)
    init_config()
    connect_to_db()
    handler()
```

### Desired Config Management Example

```python
"""
Simple, typed config with more property types using pydantic-settings.
Env vars use APP_ prefix, e.g. APP_PORT, APP_ALLOWED_ORIGINS, etc.
"""

from enum import Enum
from typing import Annotated, Literal

from pydantic import AnyUrl, Field, SecretStr, ValidationError
from pydantic_settings import BaseSettings, SettingsConfigDict

class Environment(str, Enum):
    local = "local"
    dev = "dev"
    stage = "stage"
    prod = "prod"

class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_prefix="APP_", env_file=".env")

    # strings / enums / literals
    service_name: str = "my-service"
    env: Environment = Environment.local
    log_level: Literal["DEBUG", "INFO", "WARNING", "ERROR"] = "INFO"

    # ints / constrained ints
    port: Annotated[int, Field(ge=1, le=65535)] = 8000
    worker_count: Annotated[int, Field(ge=1, le=64)] = 4

    # floats
    request_timeout_seconds: Annotated[float, Field(gt=0)] = 10.0

    # bools
    debug: bool = False
    auth_enabled: bool = True

    # URLs
    database_url: str = Field(..., description="DB DSN/URL (required)")
    upstream_api_base_url: AnyUrl = Field(..., description="Base URL for upstream API")

    # lists (comma-separated in env: "https://a.com,https://b.com")
    allowed_origins: list[AnyUrl] = Field(default_factory=list)

    # dicts (set via JSON in env: APP_FEATURE_FLAGS='{"new_ui": true}')
    feature_flags: dict[str, bool] = Field(default_factory=dict)

    # secrets (redacted on print)
    api_key: SecretStr = Field(..., description="External API key (required)")

def main() -> None:
    try:
        s = Settings()
    except ValidationError as e:
        raise SystemExit(f"Invalid configuration:\n{e}")

    print(
        "Loaded settings:",
        {
            "service_name": s.service_name,
            "env": s.env.value,
            "log_level": s.log_level,
            "port": s.port,
            "worker_count": s.worker_count,
            "request_timeout_seconds": s.request_timeout_seconds,
            "debug": s.debug,
            "auth_enabled": s.auth_enabled,
            "upstream_api_base_url": str(s.upstream_api_base_url),
            "allowed_origins": [str(u) for u in s.allowed_origins],
            "feature_flags": s.feature_flags,
            "api_key": str(s.api_key),  # prints as **********
        },
    )

if __name__ == "__main__":
    main()
```