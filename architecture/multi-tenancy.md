# Multi-Tenancy Guide

## When to Use

Any B2B SaaS where each customer (organization, university, company) needs isolated data.

## Strategy: Row-Level Security (RLS)

Single database, shared schema, `TenantId` column on every table.

### Why RLS over separate databases?

| Approach | Pros | Cons |
|----------|------|------|
| **Separate DB per tenant** | Full isolation, easy backup/restore per tenant | Expensive, hard to manage migrations, connection pooling nightmare |
| **Separate schema per tenant** | Good isolation, same DB | Migration complexity, dynamic schema routing |
| **RLS (shared schema)** | Simple, cost-effective, easy migrations | Must be rigorous about filtering, noisy neighbor risk |

For most projects, **RLS is the right default**. Switch to separate DBs only when regulatory requirements demand it.

## Implementation Checklist

### 1. Every Entity Has TenantId

```csharp
public abstract class BaseEntity
{
    public Guid Id { get; set; }
    public Guid TenantId { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime? UpdatedAt { get; set; }
}
```

**No exceptions.** If an entity doesn't have `TenantId`, it's a bug.

### 2. Tenant Resolution Middleware

Resolve tenant from the request before any data access:

```
Request → TenantMiddleware → Resolve TenantId → Set in context → Continue
```

Resolution strategies (pick one or combine):
- **Subdomain**: `acme.app.com` → TenantId for Acme
- **Header**: `X-Tenant-Id: <guid>`
- **JWT claim**: `tenant_id` in the access token
- **Path**: `/api/tenants/{id}/...` (less clean)

### 3. Global Query Filter

In EF Core, apply a global filter so every query is automatically scoped:

```csharp
modelBuilder.Entity<Course>()
    .HasQueryFilter(e => e.TenantId == _currentTenant.Id);
```

This is your safety net — even if a developer forgets to filter, the global filter catches it.

### 4. Tenant Scoping in Every Layer

| Layer | How |
|-------|-----|
| **API** | Middleware resolves tenant, sets in scoped service |
| **Application** | Handlers receive tenant from DI, pass to repositories |
| **Infrastructure** | EF global query filter + explicit checks on write |
| **External services** | Prefix keys/buckets with tenant (Redis: `tenant:{id}:...`, MinIO: `tenant-{id}`) |

### 5. Cross-Tenant Prevention

- **Never** expose an API that lists data across tenants (except super-admin)
- **Always** validate that the resource belongs to the current tenant on update/delete
- **Audit** cross-tenant access attempts (log as security event)

## External Service Isolation

| Service | Isolation Strategy |
|---------|-------------------|
| PostgreSQL | Global query filter with TenantId |
| Redis | Key prefix: `tenant:{tenantId}:cache:...` |
| MinIO/S3 | Bucket per tenant: `tenant-{tenantId}` |
| Qdrant | Collection per course (already tenant-scoped via course ownership) |
| ClickHouse | TenantId column in every event table |

## Testing Multi-Tenancy

Critical tests to write:
1. **Tenant A cannot see Tenant B's data** — Create data for two tenants, query as each, verify isolation
2. **Missing TenantId fails** — Requests without tenant context should be rejected (401/403)
3. **Write operations validate tenant** — Updating a resource from another tenant should fail
4. **Global filter works** — Raw query returns all data, but EF query returns only current tenant's data
