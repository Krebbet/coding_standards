# Configuration and Secrets Management in Cloud Environments

Managing configuration and secrets correctly is one of the most critical and most frequently mishandled aspects of cloud application development. This document covers the authoritative patterns for Python-based ML/DS services.

## The Core Problem

ML projects accumulate secrets quickly: cloud storage credentials, database connection strings, API keys for data vendors, model registry tokens, experiment tracker API keys, Slack webhook URLs for alerting. The wrong approach — hardcoding these in source files or notebooks, committing `.env` files, or passing them as command-line arguments — leads to credential leaks and non-portable deployments.

## Configuration Hierarchy

A well-structured application reads configuration in a clear precedence order:

1. **Defaults in code** — safe, non-sensitive defaults (e.g., `LOG_LEVEL=INFO`, `BATCH_SIZE=32`)
2. **Environment-specific config files** — non-secret values that vary by environment (e.g., `APP_ENV=production`, `FEATURE_STORE_URL=...`)
3. **Environment variables** — the standard injection point for all config in containerized apps
4. **Secrets manager** — for actual secrets, injected at runtime from a managed secrets service

## Never Do This

```python
# BAD: secrets in source code
AWS_SECRET_KEY = "AKIAIOSFODNN7EXAMPLE"
DB_PASSWORD = "hunter2"

# BAD: secrets in config files committed to git
# config/production.yaml:
# database:
#   password: hunter2
```

Add `.env`, `*.pem`, `*credentials*`, and `config/secrets.*` to `.gitignore`. Use `git-secrets` or `truffleHog` in CI to catch accidental commits.

## The Right Pattern: Environment Variables + Secrets Manager

### In Python: pydantic-settings

`pydantic-settings` provides a clean, type-safe way to read configuration from environment variables:

```python
from pydantic_settings import BaseSettings
from pydantic import SecretStr

class Settings(BaseSettings):
    app_env: str = "development"
    model_bucket: str = "my-models-dev"
    db_url: str = "postgresql://localhost/mydb"
    api_key: SecretStr  # required, no default — will raise if missing

    class Config:
        env_file = ".env"  # for local development only

settings = Settings()
```

`SecretStr` prevents the value from being printed in logs or `repr()` output, which is important for audit compliance.

### AWS Secrets Manager Pattern

For production workloads, secrets should live in a managed service (AWS Secrets Manager, GCP Secret Manager, HashiCorp Vault) and be fetched at application startup:

```python
import boto3
import json

def get_secret(secret_name: str) -> dict:
    client = boto3.client("secretsmanager", region_name="us-east-1")
    response = client.get_secret_value(SecretId=secret_name)
    return json.loads(response["SecretString"])

# At startup
db_creds = get_secret("prod/myapp/db-credentials")
DB_URL = f"postgresql://{db_creds['username']}:{db_creds['password']}@{db_creds['host']}/mydb"
```

For Kubernetes, use the AWS Secrets and Config Provider (ASCP) or External Secrets Operator to sync secrets into Kubernetes Secrets automatically, so your pods never need boto3 calls — the secrets arrive as env vars.

## Kubernetes-Specific Patterns

Kubernetes Secrets should be used to inject credentials into pods as environment variables or mounted files. Do not store raw secrets in `values.yaml` or Helm charts in git. Instead:

- Use **External Secrets Operator** to sync from AWS Secrets Manager / GCP Secret Manager into Kubernetes Secrets
- Use **Sealed Secrets** if you must commit encrypted secret manifests to git
- Reference secrets in pod specs via `envFrom` or `volumeMounts`, not in the image itself

```yaml
# pod spec excerpt
env:
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: myapp-db-secret
        key: password
```

## Separating Config from Secrets

Not all config is sensitive. Bucket names, feature flag values, model version strings, and timeouts are configuration (not secrets) and can live in:

- Environment variables set in CI/CD pipeline or Kubernetes ConfigMaps
- A config file committed to git (when it contains no credentials)
- AWS Parameter Store (for simple key-value config, cheaper than Secrets Manager)

The rule: if rotating it requires a security incident response, it's a secret. Otherwise it's config.

## Local Development

For local development, use a `.env` file (never committed) with placeholder or dev-specific values:

```bash
# .env (in .gitignore)
APP_ENV=development
MODEL_BUCKET=my-models-dev
DB_URL=postgresql://localhost/mydb_dev
API_KEY=dev-key-not-real
```

Load it with `python-dotenv` or `pydantic-settings`'s `env_file` support. Provide a `.env.example` with placeholder values committed to the repo so teammates know what variables are needed.

## Secrets Rotation

Design applications to tolerate secrets rotation without downtime:
- Fetch secrets at startup and re-fetch on a schedule or on credential errors
- AWS Secrets Manager supports automatic rotation for RDS credentials natively
- Build retry logic around `SecretValueNotFound` exceptions during rotation windows

## Source

- https://12factor.net/config — The Twelve-Factor App: Config factor
- https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html — AWS Secrets Manager documentation
- https://cloud.google.com/secret-manager/docs/overview — GCP Secret Manager overview
- https://external-secrets.io/ — External Secrets Operator for Kubernetes
- https://docs.pydantic.dev/latest/concepts/pydantic_settings/ — pydantic-settings documentation
