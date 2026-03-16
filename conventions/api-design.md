# REST API Design Standards

## URL Structure

```
https://api.example.com/api/{resource}
```

- **Plural nouns** for resources: `/courses`, `/users`, `/enrollments`
- **kebab-case** for multi-word: `/course-categories`, `/audit-logs`
- **No verbs** in URLs — use HTTP methods instead
- **Nesting** for relationships: `/courses/{id}/students`
- **Max 2 levels** of nesting. Beyond that, use query params or top-level resource.

## HTTP Methods

| Method | Purpose | Idempotent | Request Body | Response |
|--------|---------|------------|-------------|----------|
| `GET` | Read resource(s) | Yes | No | 200 + data |
| `POST` | Create resource | No | Yes | 201 + created resource |
| `PUT` | Full update | Yes | Yes | 200 + updated resource |
| `PATCH` | Partial update | Yes | Yes (partial) | 200 + updated resource |
| `DELETE` | Remove resource | Yes | No | 204 (no content) |

## Response Format

### Success (single resource)

```json
{
  "id": "abc-123",
  "title": "Introduction to Psychology",
  "createdAt": "2025-03-10T14:30:00Z"
}
```

### Success (collection)

```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalPages": 5,
    "totalCount": 98
  }
}
```

### Error

See [api-error-format.md](api-error-format.md) for the standard error format.

## Pagination

**Offset-based** (default for most use cases):

```
GET /api/courses?page=2&pageSize=20
```

| Parameter | Default | Max | Description |
|-----------|---------|-----|-------------|
| `page` | 1 | — | Page number (1-based) |
| `pageSize` | 20 | 100 | Items per page |

Response includes pagination metadata in the response body (see above).

**Cursor-based** (for real-time feeds, infinite scroll):

```
GET /api/notifications?cursor=eyJpZCI6MTIzfQ&limit=20
```

Use cursor-based when:
- Data changes frequently (new items inserted)
- You need consistent pagination without duplicates
- Large datasets where OFFSET is slow

## Filtering

Use query parameters:

```
GET /api/courses?status=active&departmentId=5&search=psychology
```

- **Exact match**: `?status=active`
- **Search/partial**: `?search=psych` (searches relevant text fields)
- **Date range**: `?createdAfter=2025-01-01&createdBefore=2025-12-31`
- **Multiple values**: `?status=active,archived` (comma-separated)

## Sorting

```
GET /api/courses?sortBy=createdAt&sortOrder=desc
```

| Parameter | Values | Default |
|-----------|--------|---------|
| `sortBy` | Field name | `createdAt` |
| `sortOrder` | `asc`, `desc` | `desc` |

For multiple sort fields: `?sortBy=status,-createdAt` (prefix `-` for descending).

## Versioning

Prefer **URL path versioning** when breaking changes are needed:

```
/api/v1/courses
/api/v2/courses
```

Rules:
- Don't version from day one — start with `/api/courses`
- Add versioning only when you make a breaking change
- Support the previous version for a deprecation period
- Document the migration path

## Rate Limiting

Return these headers on every response:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1620000000
```

When exceeded, return `429 Too Many Requests` with `Retry-After` header.

## Common Patterns

### Bulk Operations

```
POST /api/courses/bulk-delete
Body: { "ids": ["abc", "def", "ghi"] }
```

### Actions (non-CRUD operations)

```
POST /api/courses/{id}/publish
POST /api/courses/{id}/archive
POST /api/users/{id}/deactivate
```

### Health Check

```
GET /api/health          → 200 { "status": "healthy" }
GET /api/health/ready    → 200 or 503 (checks dependencies)
```

## Response Codes Cheat Sheet

| Code | When |
|------|------|
| `200 OK` | Successful GET, PUT, PATCH |
| `201 Created` | Successful POST (include `Location` header) |
| `204 No Content` | Successful DELETE |
| `400 Bad Request` | Validation error, malformed request |
| `401 Unauthorized` | Missing or invalid authentication |
| `403 Forbidden` | Authenticated but not authorized |
| `404 Not Found` | Resource doesn't exist |
| `409 Conflict` | Business rule violation (duplicate, capacity full) |
| `422 Unprocessable Entity` | Valid JSON but semantically wrong |
| `429 Too Many Requests` | Rate limit exceeded |
| `500 Internal Server Error` | Unhandled server error (never expose details) |
