# Database Conventions

## Schema Design

### Naming

| Element | Convention | Example |
|---------|------------|---------|
| Tables | PascalCase, plural | `Courses`, `EnrollmentRecords` |
| Columns | PascalCase | `CreatedAt`, `TenantId`, `IsActive` |
| Primary keys | `Id` (Guid or int) | `Id` |
| Foreign keys | `<Entity>Id` | `CourseId`, `UserId` |
| Indexes | `IX_<Table>_<Columns>` | `IX_Courses_TenantId` |
| Unique constraints | `UQ_<Table>_<Columns>` | `UQ_Users_Email_TenantId` |
| FK constraints | `FK_<Child>_<Parent>` | `FK_Enrollments_Courses` |

### Standard Columns

Every table should have these base columns:

```sql
Id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
TenantId    UUID NOT NULL,          -- multi-tenancy (if applicable)
CreatedAt   TIMESTAMPTZ NOT NULL DEFAULT now(),
UpdatedAt   TIMESTAMPTZ NULL,
CreatedBy   UUID NULL,              -- optional: who created it
IsDeleted   BOOLEAN NOT NULL DEFAULT false  -- soft delete (if needed)
```

### Data Types

| Use | PostgreSQL | Avoid |
|-----|-----------|-------|
| IDs | `UUID` | `SERIAL` (unless performance-critical) |
| Timestamps | `TIMESTAMPTZ` | `TIMESTAMP` (no timezone = bugs) |
| Money | `DECIMAL(18,2)` | `FLOAT`, `REAL` (precision loss) |
| Booleans | `BOOLEAN` | `INT` with 0/1 |
| Enums | `SMALLINT` + app-level enum | `VARCHAR` (typo-prone), `PG ENUM` (hard to migrate) |
| Text | `VARCHAR(N)` with limit | `TEXT` without limit (unbounded = risk) |
| JSON | `JSONB` | `JSON` (not indexable) |

## Migrations

### Rules

1. **Always use migrations** — Never `EnsureCreated()` or manual DDL in production
2. **Migrations are forward-only** — Never edit a migration after it has been applied
3. **One migration per change** — Don't bundle unrelated schema changes
4. **Test rollback** — Every migration should be reversible (down migration)
5. **Name descriptively** — `AddMaxCapacityToCourses`, not `Update1` or `Migration_003`

### Dangerous Operations

These require extra caution (can cause downtime or data loss):

| Operation | Risk | Safe Alternative |
|-----------|------|-----------------|
| Drop column | Data loss | Add `IsDeleted`/rename first, drop later |
| Rename column | Breaks running code | Add new column → copy data → drop old (multi-step) |
| Add NOT NULL column | Fails if table has data | Add as nullable → backfill → alter to NOT NULL |
| Change column type | Data truncation | Add new column → migrate data → swap |
| Drop table | Data loss | Rename to `_deprecated_` first, drop after verification |
| Add index on large table | Locks table | `CREATE INDEX CONCURRENTLY` (PostgreSQL) |

### Migration in CI

- Run migrations as part of the deployment pipeline
- Test migrations against a copy of production data (or realistic test data)
- Never run migrations manually in production — always automated

## Indexing

### When to Add an Index

- Columns used in `WHERE` clauses frequently
- Columns used in `JOIN` conditions
- Columns used in `ORDER BY`
- Foreign key columns (PostgreSQL doesn't auto-index FKs)
- `TenantId` — always index this (appears in every query)

### When NOT to Add an Index

- Small tables (< 1000 rows) — sequential scan is faster
- Columns with very low cardinality (boolean, status with 3 values)
- Write-heavy tables where read performance isn't critical

### Composite Index Order

Put the most selective column first:

```sql
-- Good: TenantId first (narrows the search), then status
CREATE INDEX IX_Courses_Tenant_Status ON Courses (TenantId, Status);

-- Query benefits from this index:
SELECT * FROM Courses WHERE TenantId = @tid AND Status = 'active';
```

## Query Performance

1. **Use `EXPLAIN ANALYZE`** to verify query plans before deploying
2. **Avoid `SELECT *`** — project only the columns you need
3. **Use pagination** — never return unbounded result sets
4. **Avoid N+1 queries** — use `JOIN`, `Include()`, or batch loading
5. **Use `AsNoTracking()`** for read-only queries in EF Core
6. **Connection pooling** — always use a connection pool (default in EF Core)

## Backups

- **Automated daily backups** with point-in-time recovery
- **Test restore** at least monthly — untested backups are not backups
- **Retain** 7 daily + 4 weekly + 3 monthly backups
- **Store offsite** — backups on the same server as the DB are useless if the server dies

## Seed Data

- Use seed data for development only (test accounts, sample data)
- Never seed production databases with test data
- Store seed scripts in `Infrastructure/Persistence/Seeds/`
- Seed data should be idempotent (safe to run multiple times)
