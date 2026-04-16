# Configuration Management

Runtime configuration must be **explicit, typed, validated, and environment-specific**. Use `pydantic-settings` to load environment variables into a single `Settings` object. Secrets never live in code or logs.

## Core Principles

| Principle | What It Means |
|---|---|
| Single source of truth | One `Settings` model per service; no `os.getenv()` scattered in business logic |
| Typed configuration | Every config value has an explicit type (`int` ports, `AnyUrl`, `bool` flags, enums) |
| Fail fast | Missing/invalid required config stops startup with a clear error |
| Secure by default | Secrets from env or secret managers; never committed or logged |
| Predictable precedence | Document the override order (env vars > `.env` file > defaults) |

## Standard Pattern

```python
from pydantic import AnyUrl, Field, SecretStr
from pydantic_settings import BaseSettings, SettingsConfigDict
from enum import Enum

class Environment(str, Enum):
    local = "local"
    dev = "dev"
    stage = "stage"
    prod = "prod"

class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_prefix="APP_", env_file=".env")

    env: Environment = Environment.local
    log_level: str = "INFO"
    port: int = Field(default=8000, ge=1, le=65535)
    debug: bool = False
    database_url: str = Field(..., description="DB connection string (required)")
    api_key: SecretStr = Field(..., description="External API key (required)")

def get_settings() -> Settings:
    try:
        return Settings()
    except Exception as e:
        raise SystemExit(f"Invalid configuration:\n{e}")
```

Instantiate settings **once at startup** and inject them as a dependency. Do not call `Settings()` repeatedly or read `os.getenv()` in business logic.

## What Goes in Environment Variables

**Use env vars for:**
- Deployment-specific values: env name, service URLs, ports, timeouts, feature flags
- Credentials and secrets (ideally injected from a secret manager at runtime)
- Operational tuning: log level, tracing endpoints, pool sizes

**Do not use env vars for:**
- Large structured config blobs (use versioned config files with change control)
- Per-request or per-user settings
- Values that must be consistent across all environments (put them in code)

## Naming Conventions

- Uppercase snake case: `APP_DATABASE_URL`, `APP_LOG_LEVEL`
- Consistent prefix per service: `BILLING_…`, `SEARCH_…`
- Prefer positive booleans: `APP_AUTH_ENABLED=true` not `DISABLE_AUTH=false`

## Secrets Handling

- Never print or log the full settings object (raw secrets will appear)
- Use `pydantic`'s `SecretStr` — it renders as `**********` in logs
- Production secrets come from a secret store (not `.env` files)
- Keep `.env` out of git; commit only `.env.example` with placeholder values

## Local Development

```
# .env (git-ignored)
APP_DATABASE_URL=postgresql://localhost/mydb
APP_API_KEY=dev-key-do-not-use-in-prod

# .env.example (committed)
APP_DATABASE_URL=          # DB DSN/URL
APP_API_KEY=               # External API key
```

## Testing

Construct settings with explicit values in tests to avoid coupling to developer machine environments:

```python
settings = Settings(database_url="sqlite:///:memory:", api_key="test-key")
```

## Source
- `../raw/SFL Software Design Principles/Robust Configuration Management…md`

## Related
- [[principles/logging]]
- [[principles/io-contracts]]
