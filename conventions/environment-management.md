# Environment Management

## Environments

| Environment | Purpose | Deploys From | Who Uses It |
|-------------|---------|-------------|-------------|
| **Local** | Development | Developer machine | Individual developer |
| **Staging** | Pre-production testing | `main` branch (auto) | Team, QA |
| **Production** | Live users | Version tags (manual trigger) | Everyone |

### Optional Environments

| Environment | When to Use |
|-------------|-------------|
| **Preview** | Per-PR deployments for review (Vercel, Netlify) |
| **QA** | Dedicated QA testing with controlled data |
| **Load Test** | Performance testing with production-like data |

## Configuration Management

### Environment Variables

Use `.env` files for local development, environment variables or secrets managers for deployed environments.

```text
.env                  → Local defaults (git-ignored)
.env.example          → Template with placeholder values (committed)
.env.test             → Test-specific overrides (committed, no secrets)
```

### .env.example

Always maintain a `.env.example` with every variable documented:

```bash
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/mydb

# Redis
REDIS_URL=redis://localhost:6379

# Auth
JWT_SECRET=your-secret-here
JWT_EXPIRY_MINUTES=15

# External Services
STORAGE_BUCKET=my-bucket
STORAGE_ENDPOINT=http://localhost:9000

# Feature Flags
FEATURE_NEW_DASHBOARD=false
FEATURE_AI_CHAT=false

# App
APP_URL=http://localhost:3000
API_URL=http://localhost:5000
LOG_LEVEL=Information
```

### Rules

- **Never commit `.env` files** with real values
- **Every new env var** must be added to `.env.example` in the same PR
- **Use descriptive names**: `DATABASE_URL` not `DB`, `JWT_SECRET` not `SECRET`
- **Document expected format** in comments when not obvious
- **Group by category** (database, auth, external services, features)

### Accessing Config

```csharp
// .NET — Use Options pattern, never read env vars directly in services
public class JwtSettings
{
    public string Secret { get; set; }
    public int ExpiryMinutes { get; set; }
}

// Registration
builder.Services.Configure<JwtSettings>(builder.Configuration.GetSection("Jwt"));
```

```typescript
// TypeScript — Validate at startup, fail fast
import { z } from 'zod';

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  NODE_ENV: z.enum(['development', 'test', 'production']),
});

export const env = envSchema.parse(process.env);
```

```python
# Python — Use pydantic-settings
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    database_url: str
    jwt_secret: str
    log_level: str = "INFO"

    class Config:
        env_file = ".env"

settings = Settings()
```

## Feature Flags

Use feature flags to control feature rollout without redeploying.

### When to Use

- Releasing a feature incrementally (% of users)
- Gating a feature behind a tenant or role
- Quick kill switch for risky features
- A/B testing

### Implementation

```typescript
// Simple env-based flags (good for small projects)
const features = {
  newDashboard: process.env.FEATURE_NEW_DASHBOARD === 'true',
  aiChat: process.env.FEATURE_AI_CHAT === 'true',
};

if (features.newDashboard) {
  // render new dashboard
}
```

```csharp
// .NET — Feature Management library
builder.Services.AddFeatureManagement();

// Usage
if (await _featureManager.IsEnabledAsync("NewDashboard"))
{
    // new behavior
}
```

### Rules

- **Name clearly**: `FEATURE_NEW_DASHBOARD` not `FLAG_1`
- **Default to off** for new features
- **Clean up after rollout** — remove the flag and dead code path once the feature is fully launched
- **Log flag evaluations** — know which users see which features
- **Document every flag** with purpose, owner, and expected removal date

### Flag Lifecycle

```text
Created → Testing → Rollout (10% → 50% → 100%) → Cleanup (remove flag + old code)
```

## Secrets Management

### By Environment

| Environment | Strategy |
|-------------|----------|
| **Local** | `.env` file (git-ignored) |
| **CI/CD** | GitHub Actions Secrets / pipeline variables |
| **Staging** | Secrets manager (Azure Key Vault, AWS Secrets Manager, Doppler) |
| **Production** | Secrets manager with audit logging and rotation |

### Rules

- **Never hardcode secrets** in source code
- **Rotate secrets** after team member departures
- **Use different secrets per environment** — staging and production must never share
- **Audit access** — know who can read production secrets
- **Least privilege** — each service gets only the secrets it needs

## Environment Parity

Keep environments as similar as possible to catch issues early.

### What Must Match

- Same database engine and version (PostgreSQL 16, not SQLite for dev)
- Same cache engine (Redis, not in-memory for dev)
- Same message broker
- Same container runtime

### What Can Differ

- Resource allocation (fewer replicas, smaller instances)
- External service endpoints (sandbox APIs vs production)
- Data volume (subset of production data, anonymized)
- Domain names and certificates

### Docker Compose for Local Parity

```yaml
# docker-compose.yml — mirrors production dependencies
services:
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: dev
      POSTGRES_PASSWORD: dev
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  minio:
    image: minio/minio:latest
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: minioadmin
      MINIO_ROOT_PASSWORD: minioadmin
    ports:
      - "9000:9000"
      - "9001:9001"
```

## Anti-Patterns

- **"Works on my machine"** — Use Docker Compose to ensure local parity with production
- **Shared staging database** — Each PR/branch should ideally get isolated data
- **Secrets in code or git history** — If it happened, rotate the secret immediately
- **No `.env.example`** — New developers shouldn't have to guess what variables are needed
- **Feature flags that live forever** — Set a removal date when creating the flag
