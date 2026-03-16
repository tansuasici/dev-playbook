# Observability Standards

Logging alone is not enough. A production system needs **metrics**, **tracing**, and **alerting** to be truly observable.

> See also: [logging.md](logging.md) for structured logging standards.

## Three Pillars

| Pillar | What It Tells You | Tools |
|--------|-------------------|-------|
| **Logs** | What happened (discrete events) | Seq, ELK, Grafana Loki |
| **Metrics** | How the system is performing (aggregated numbers) | Prometheus, Grafana, InfluxDB |
| **Traces** | How a request flows across services (distributed) | Jaeger, Zipkin, OpenTelemetry |

## Health Checks

Every service must expose health endpoints.

### Endpoints

```
GET /api/health          → 200 { "status": "healthy" }
GET /api/health/ready    → 200 or 503 (checks dependencies)
GET /api/health/live     → 200 (process is running, used by k8s liveness probe)
```

### What Readiness Checks

| Dependency | Check |
|-----------|-------|
| Database | Can connect and run a simple query |
| Redis/Cache | Can connect and ping |
| External API | Can reach the endpoint (with timeout) |
| Message Queue | Can connect to broker |
| File Storage | Can list/read from bucket |

```csharp
// .NET
builder.Services.AddHealthChecks()
    .AddNpgSql(connectionString, name: "postgres")
    .AddRedis(redisConnection, name: "redis")
    .AddUrlGroup(new Uri("https://external-api/health"), name: "external-api");
```

```typescript
// Next.js / Express
app.get('/api/health/ready', async (req, res) => {
  const checks = {
    database: await checkDatabase(),
    redis: await checkRedis(),
    storage: await checkStorage(),
  };

  const healthy = Object.values(checks).every(Boolean);
  res.status(healthy ? 200 : 503).json({ status: healthy ? 'healthy' : 'unhealthy', checks });
});
```

## Metrics

### What to Measure

| Metric | Type | Example |
|--------|------|---------|
| **Request rate** | Counter | `http_requests_total` |
| **Request duration** | Histogram | `http_request_duration_seconds` |
| **Error rate** | Counter | `http_errors_total` (by status code) |
| **Active connections** | Gauge | `http_active_connections` |
| **Queue depth** | Gauge | `job_queue_size` |
| **Business metrics** | Counter | `enrollments_total`, `courses_created_total` |

### RED Method (for services)

Every service should track:
- **R**ate — requests per second
- **E**rrors — failed requests per second
- **D**uration — time per request (p50, p95, p99)

### USE Method (for resources)

Every resource (CPU, memory, disk, connections) should track:
- **U**tilization — percentage in use
- **S**aturation — queue depth / backlog
- **E**rrors — error count

### Naming Convention

```
<namespace>_<name>_<unit>

# Examples
http_request_duration_seconds
db_query_duration_seconds
cache_hits_total
job_processing_errors_total
```

## Distributed Tracing

### When You Need It

- Multiple services handle a single request
- You need to find where latency comes from
- You need to debug cross-service failures

### Implementation

Use **OpenTelemetry** as the standard — it's vendor-neutral and supports all major backends.

```csharp
// .NET
builder.Services.AddOpenTelemetry()
    .WithTracing(tracing => tracing
        .AddAspNetCoreInstrumentation()
        .AddHttpClientInstrumentation()
        .AddEntityFrameworkCoreInstrumentation()
        .AddOtlpExporter());
```

```python
# Python
from opentelemetry import trace
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor

FastAPIInstrumentor.instrument_app(app)
```

### Propagation

- Pass `traceparent` header between services (W3C Trace Context standard)
- Include `CorrelationId` in all log entries for manual correlation
- Never create custom trace ID formats — use the standard

## Alerting

### Alert Rules

| Severity | Condition | Action |
|----------|-----------|--------|
| **Critical** | Service down, error rate > 50%, data loss risk | Page on-call immediately |
| **Warning** | Error rate > 5%, latency p99 > 2s, disk > 80% | Notify in Slack, investigate within 1 hour |
| **Info** | Deployment completed, cert expiring in 30 days | Log for awareness |

### Alert Best Practices

- **Alert on symptoms, not causes** — "API latency > 2s" not "CPU > 80%"
- **Include runbook links** in alert messages
- **Set appropriate thresholds** — avoid alert fatigue from noisy alerts
- **Use alert grouping** — don't fire 100 alerts for the same incident
- **Test alerts** — regularly verify they fire when expected

### Alert Template

```yaml
alert: HighErrorRate
expr: rate(http_errors_total{status=~"5.."}[5m]) / rate(http_requests_total[5m]) > 0.05
for: 5m
labels:
  severity: warning
annotations:
  summary: "High error rate on {{ $labels.service }}"
  description: "Error rate is {{ $value | humanizePercentage }} over the last 5 minutes"
  runbook: "https://wiki.internal/runbooks/high-error-rate"
```

## Dashboards

### Every Service Dashboard Should Have

1. **Request rate** — traffic over time
2. **Error rate** — 4xx and 5xx separately
3. **Latency** — p50, p95, p99 over time
4. **Saturation** — CPU, memory, connections, queue depth
5. **Business metrics** — relevant domain-specific counters

### Dashboard Rules

- One dashboard per service, not one giant dashboard
- Use consistent time ranges and refresh intervals
- Include links to related dashboards and runbooks
- Mark deployment events on graphs for correlation

## Anti-Patterns

- **Logging as the only observability** — Logs alone can't show trends or distributions
- **No health checks** — You can't know if a service is healthy without asking it
- **Alerting on every metric** — Alert fatigue leads to ignored alerts
- **Custom metrics libraries** — Use OpenTelemetry, don't reinvent the wheel
- **Dashboard without context** — Every graph should have a title that explains what "bad" looks like
