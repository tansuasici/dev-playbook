# Docker Conventions

## Dockerfile Best Practices

### Multi-Stage Builds

Always use multi-stage builds to keep images small:

```dockerfile
# Stage 1: Build
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src
COPY *.sln .
COPY src/**/*.csproj ./
RUN dotnet restore
COPY . .
RUN dotnet publish -c Release -o /app/publish

# Stage 2: Runtime
FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS runtime
WORKDIR /app
COPY --from=build /app/publish .
EXPOSE 8080
ENTRYPOINT ["dotnet", "Api.dll"]
```

### Rules

1. **Pin base image versions** — `node:22-alpine`, not `node:latest`
2. **Use alpine variants** when possible — smaller image, smaller attack surface
3. **Don't run as root** — Add a non-root user:

   ```dockerfile
   RUN adduser --disabled-password --gecos "" appuser
   USER appuser
   ```

4. **Order layers by change frequency** — Dependencies first (cached), source code last
5. **Use .dockerignore** — Exclude `node_modules/`, `bin/`, `obj/`, `.git/`, `.env`
6. **One process per container** — Don't run web server + background worker in the same container
7. **Health checks in Dockerfile**:

   ```dockerfile
   HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
     CMD curl -f http://localhost:8080/health || exit 1
   ```

## Docker Compose

### Development Stack Template

```yaml
services:
  postgres:
    image: postgres:17-alpine
    environment:
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: ${DB_NAME}
    ports:
      - "${DB_PORT:-5432}:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:8-alpine
    ports:
      - "${REDIS_PORT:-6379}:6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
  redis_data:
```

### Compose Rules

1. **Use `.env` file** for all configurable values (ports, credentials, versions)
2. **Add health checks** to every service — dependent services use `depends_on: condition: service_healthy`
3. **Named volumes** for persistent data — never bind-mount database data directories
4. **Pin image versions** — `postgres:17-alpine`, not `postgres:latest`
5. **Don't expose ports in production** that should be internal-only

### Environment Variables

```bash
# .env.example (commit this)
DB_USER=myapp
DB_PASSWORD=change_me
DB_NAME=myapp_dev
DB_PORT=5432
REDIS_PORT=6379

# .env (do NOT commit)
DB_USER=myapp
DB_PASSWORD=actual_secure_password
DB_NAME=myapp_dev
DB_PORT=5432
REDIS_PORT=6379
```

## Image Naming

```text
ghcr.io/<org>/<service>:<tag>
```

| Tag | When | Example |
|-----|------|---------|
| `latest` | Latest build from main | `ghcr.io/org/api:latest` |
| `sha-<hash>` | Specific commit | `ghcr.io/org/api:sha-abc1234` |
| `v1.2.0` | Release version | `ghcr.io/org/api:v1.2.0` |

## .dockerignore

```text
.git
.github
node_modules
bin
obj
.env
.env.*
*.md
tests
**/*.test.*
.DS_Store
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Running as root | Add `USER` instruction |
| `COPY . .` before restore/install | Copy dependency files first, then `COPY . .` |
| No .dockerignore | Add one — `node_modules` in an image is a build time disaster |
| `latest` tag in production | Pin to specific version or SHA |
| Secrets in build args | Use runtime env vars or secrets mount |
| Giant images | Use multi-stage + alpine |
