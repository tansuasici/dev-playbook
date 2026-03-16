# CI/CD Standards

## Core Principle

**CI is the gatekeeper. If CI fails, you don't merge. Period.**

## CI Pipeline Requirements

Every project must have a CI pipeline that runs on:

- Push to `main`
- Pull requests targeting `main`

### Minimum CI Steps by Stack

#### .NET Projects

```yaml
steps:
  - Checkout
  - Setup .NET SDK
  - Restore packages (with cache)
  - Format check (dotnet format --verify-no-changes)
  - Build (dotnet build --no-restore)
  - Unit tests (dotnet test --filter Category!=Integration)
  - Integration tests (with service containers: PostgreSQL, Redis, etc.)
```

#### Next.js Projects

```yaml
steps:
  - Checkout
  - Setup Node.js (with npm cache)
  - Install (npm ci)
  - Lint (npm run lint)
  - Type check (npx tsc --noEmit)
  - Unit tests (npm test -- --run)
  - Build (npm run build)
```

#### Python Projects

```yaml
steps:
  - Checkout
  - Setup Python
  - Install dependencies (pip install -r requirements.txt)
  - Lint (ruff check .)
  - Format check (ruff format --check .)
  - Type check (mypy .)
  - Tests (pytest -v)
```

## CI Rules

1. **Tests MUST block the pipeline** — Never use `continue-on-error: true` or `|| true` on test steps. If tests aren't stable enough to be blocking, fix the tests.
2. **Keep CI fast** — Under 5 minutes for unit tests, under 10 for the full pipeline. Slow CI gets ignored.
3. **Cache dependencies** — Use GitHub Actions cache for NuGet, npm, pip. Saves minutes per run.
4. **Use service containers** — For integration tests, spin up real PostgreSQL/Redis/etc. in CI. Don't mock infrastructure.
5. **Fail fast** — Put linting and formatting first. No point running tests if the code doesn't compile.
6. **Pin versions** — Pin SDK, runtime, and action versions. `actions/checkout@v4`, not `@latest`.
7. **Concurrency control** — Cancel in-progress runs for the same branch. Don't waste compute.

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

## CD Pipeline

### Staging

- Auto-deploy on merge to `main`
- Docker build → push to registry → deploy to staging server
- Run smoke tests after deploy

### Production

- Deploy on version tags (`v*`)
- Same image that passed staging
- Rollback plan documented

### Environment Variables

- Never hardcode secrets in CI files
- Use GitHub Secrets for sensitive values
- Use GitHub Variables for non-sensitive config
- Validate environment variables exist before using them

## Reusable Workflows

For organizations with multiple repos, centralize CI logic:

```yaml
# In the calling repo's .github/workflows/ci.yml
jobs:
  ci:
    uses: <org>/.github/.github/workflows/dotnet-ci.yml@main
    with:
      dotnet-version: '10.0.x'
      project-path: 'src/Api'
    secrets: inherit
```

Benefits:

- Update CI once, all repos get the change
- Consistent standards across projects
- Less copy-paste drift between repos

## GitHub Actions Maintenance

- **Review action versions** quarterly — update `@v4` to `@v5` when available
- **Watch for deprecation warnings** — Node.js runtime updates, action EOL notices
- **Don't duplicate workflows** — One CI per purpose. If two workflows build the same Docker image, consolidate.
