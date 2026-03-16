# API Error Response Format

## Standard: RFC 7807 (Problem Details)

Use a consistent error format across all APIs, based on [RFC 7807](https://www.rfc-editor.org/rfc/rfc7807).

## Error Response Shape

```json
{
  "type": "https://docs.example.com/errors/validation-error",
  "title": "Validation Error",
  "status": 400,
  "detail": "One or more fields failed validation.",
  "instance": "/api/courses",
  "traceId": "00-abc123-def456-01",
  "errors": {
    "title": ["Title is required.", "Title must be under 200 characters."],
    "maxCapacity": ["Max capacity must be a positive number."]
  }
}
```

## Field Definitions

| Field | Required | Description |
|-------|----------|-------------|
| `type` | Yes | URI identifying the error type (link to docs) |
| `title` | Yes | Human-readable summary (same for all instances of this type) |
| `status` | Yes | HTTP status code |
| `detail` | Yes | Human-readable explanation specific to this occurrence |
| `instance` | No | The request path that caused the error |
| `traceId` | No | Correlation ID for debugging |
| `errors` | No | Field-level validation errors (key: field name, value: error messages array) |

## Error Types by Status Code

### 400 Bad Request — Validation Error

```json
{
  "type": "https://docs.example.com/errors/validation-error",
  "title": "Validation Error",
  "status": 400,
  "detail": "One or more fields failed validation.",
  "errors": {
    "email": ["Email is required."],
    "password": ["Password must be at least 8 characters."]
  }
}
```

### 401 Unauthorized — Authentication Required

```json
{
  "type": "https://docs.example.com/errors/unauthorized",
  "title": "Unauthorized",
  "status": 401,
  "detail": "Authentication is required to access this resource."
}
```

### 403 Forbidden — Insufficient Permissions

```json
{
  "type": "https://docs.example.com/errors/forbidden",
  "title": "Forbidden",
  "status": 403,
  "detail": "You do not have permission to delete this course."
}
```

### 404 Not Found

```json
{
  "type": "https://docs.example.com/errors/not-found",
  "title": "Not Found",
  "status": 404,
  "detail": "Course with ID 'abc-123' was not found."
}
```

### 409 Conflict — Business Rule Violation

```json
{
  "type": "https://docs.example.com/errors/conflict",
  "title": "Conflict",
  "status": 409,
  "detail": "Course has reached maximum enrollment capacity."
}
```

### 429 Too Many Requests

```json
{
  "type": "https://docs.example.com/errors/rate-limited",
  "title": "Too Many Requests",
  "status": 429,
  "detail": "Rate limit exceeded. Try again in 60 seconds."
}
```

### 500 Internal Server Error

```json
{
  "type": "https://docs.example.com/errors/internal-error",
  "title": "Internal Server Error",
  "status": 500,
  "detail": "An unexpected error occurred. Please try again later."
}
```

**Never expose stack traces, exception messages, or internal details in 500 responses.**

## Implementation

### .NET (ASP.NET Core)

ASP.NET Core has built-in Problem Details support:

```csharp
builder.Services.AddProblemDetails();

// In exception handler middleware
app.UseExceptionHandler(appBuilder =>
{
    appBuilder.Run(async context =>
    {
        var problemDetails = new ProblemDetails
        {
            Status = 500,
            Title = "Internal Server Error",
            Detail = "An unexpected error occurred."
        };
        context.Response.StatusCode = 500;
        await context.Response.WriteAsJsonAsync(problemDetails);
    });
});
```

### Next.js (API Routes)

```typescript
function createErrorResponse(status: number, title: string, detail: string, errors?: Record<string, string[]>) {
  return Response.json({
    type: `https://docs.example.com/errors/${title.toLowerCase().replace(/\s/g, '-')}`,
    title,
    status,
    detail,
    ...(errors && { errors }),
  }, { status });
}
```

## Rules

1. **Always return JSON** — Even for errors. No HTML error pages from APIs.
2. **Always include `status`** — Even though it's in the HTTP status code.
3. **Keep `detail` user-friendly** — No stack traces, no internal class names.
4. **Use `errors` object for validation** — One key per field, array of messages per key.
5. **Consistent across all endpoints** — Same shape everywhere. No surprises.
6. **Log the real error server-side** — The client gets a safe message; the server logs the full exception.
