# Error Handling Patterns

> See also: [api-error-format.md](api-error-format.md) for the standard API error response format (RFC 7807).

## Core Principle

**Handle errors at the right level.** Don't catch exceptions you can't meaningfully handle. Let them bubble up to a global handler.

## Error Categories

| Category | Example | Response Code | Handling |
|----------|---------|--------------|----------|
| **Validation** | Invalid email, missing field | 400 | Return specific field errors |
| **Authentication** | Missing/expired token | 401 | Return generic "unauthorized" |
| **Authorization** | Wrong role, wrong tenant | 403 | Return generic "forbidden" |
| **Not Found** | Resource doesn't exist | 404 | Return "not found" |
| **Business Rule** | Course full, duplicate title | 409 / 422 | Return specific business error |
| **External Service** | API timeout, service down | 502 / 503 | Retry or degrade gracefully |
| **Internal** | Unhandled exception, bug | 500 | Log full details, return generic error |

## Architecture

```text
Controller / Route Handler
  ↓ catches validation errors → 400
  ↓
Service / Use Case
  ↓ throws business exceptions → 409/422
  ↓
Repository / External Call
  ↓ throws infrastructure exceptions → 502/503
  ↓
Global Exception Handler
  ↓ catches everything else → 500 (logged, generic response)
```

## Implementation

### .NET — Exception Middleware

```csharp
// Custom exception types
public class NotFoundException : Exception
{
    public NotFoundException(string entity, object id)
        : base($"{entity} with id '{id}' was not found.") { }
}

public class BusinessRuleException : Exception
{
    public BusinessRuleException(string message) : base(message) { }
}

public class ConflictException : Exception
{
    public ConflictException(string message) : base(message) { }
}

// Global exception handler middleware
public class ExceptionHandlingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<ExceptionHandlingMiddleware> _logger;

    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (NotFoundException ex)
        {
            context.Response.StatusCode = 404;
            await WriteProblemDetails(context, "Not Found", ex.Message, 404);
        }
        catch (BusinessRuleException ex)
        {
            context.Response.StatusCode = 422;
            await WriteProblemDetails(context, "Business Rule Violation", ex.Message, 422);
        }
        catch (ConflictException ex)
        {
            context.Response.StatusCode = 409;
            await WriteProblemDetails(context, "Conflict", ex.Message, 409);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Unhandled exception for {RequestPath}", context.Request.Path);
            context.Response.StatusCode = 500;
            await WriteProblemDetails(context, "Internal Server Error",
                "An unexpected error occurred.", 500);
        }
    }
}
```

### TypeScript — Error Classes

```typescript
// Custom error classes
class AppError extends Error {
  constructor(
    public statusCode: number,
    public code: string,
    message: string,
  ) {
    super(message);
  }
}

class NotFoundError extends AppError {
  constructor(entity: string, id: string) {
    super(404, 'NOT_FOUND', `${entity} with id '${id}' not found`);
  }
}

class BusinessRuleError extends AppError {
  constructor(message: string) {
    super(422, 'BUSINESS_RULE_VIOLATION', message);
  }
}

class ConflictError extends AppError {
  constructor(message: string) {
    super(409, 'CONFLICT', message);
  }
}

// Global error handler (Express / Next.js API route wrapper)
function withErrorHandler(handler: Function) {
  return async (req: Request, res: Response) => {
    try {
      await handler(req, res);
    } catch (error) {
      if (error instanceof AppError) {
        res.status(error.statusCode).json({
          type: `https://docs.api.com/errors/${error.code.toLowerCase()}`,
          title: error.code,
          status: error.statusCode,
          detail: error.message,
        });
      } else {
        logger.error('Unhandled error', { error, path: req.url });
        res.status(500).json({
          type: 'https://docs.api.com/errors/internal',
          title: 'Internal Server Error',
          status: 500,
          detail: 'An unexpected error occurred.',
        });
      }
    }
  };
}
```

### Python — Exception Handlers

```python
# Custom exceptions
class AppException(Exception):
    def __init__(self, status_code: int, code: str, detail: str):
        self.status_code = status_code
        self.code = code
        self.detail = detail

class NotFoundException(AppException):
    def __init__(self, entity: str, id: str):
        super().__init__(404, "NOT_FOUND", f"{entity} with id '{id}' not found")

class BusinessRuleException(AppException):
    def __init__(self, detail: str):
        super().__init__(422, "BUSINESS_RULE_VIOLATION", detail)

# FastAPI exception handler
@app.exception_handler(AppException)
async def app_exception_handler(request: Request, exc: AppException):
    return JSONResponse(
        status_code=exc.status_code,
        content={
            "type": f"https://docs.api.com/errors/{exc.code.lower()}",
            "title": exc.code,
            "status": exc.status_code,
            "detail": exc.detail,
        },
    )
```

## External Service Errors

### Retry Strategy

```text
Retry 1 → wait 1s
Retry 2 → wait 2s
Retry 3 → wait 4s
Give up → return 503 or fallback
```

```csharp
// .NET — Polly retry policy
builder.Services.AddHttpClient("ExternalApi")
    .AddTransientHttpErrorPolicy(policy =>
        policy.WaitAndRetryAsync(3, attempt => TimeSpan.FromSeconds(Math.Pow(2, attempt))));
```

### Circuit Breaker

When an external service is consistently failing, stop calling it temporarily:

```text
Closed (normal) → Too many failures → Open (reject calls for 30s) → Half-Open (try one) → Closed
```

### Graceful Degradation

- If the recommendation service is down, show default content instead of an error
- If the email service is down, queue the email for later delivery
- If the search service is down, fall back to database query

## Rules

1. **Never swallow exceptions silently** — catch only if you handle it meaningfully
2. **Never expose internal details** in API responses — stack traces, SQL queries, file paths
3. **Log at the boundary** — global handler logs unhandled exceptions, not every catch block
4. **Use typed exceptions** — `NotFoundException`, not `throw new Exception("not found")`
5. **Fail fast on startup** — missing config, unreachable database = crash, don't limp along
6. **Return consistent error format** — always RFC 7807 Problem Details (see [api-error-format.md](api-error-format.md))
7. **Don't use exceptions for flow control** — check `if (course == null)` instead of catching `NullReferenceException`

## Anti-Patterns

```csharp
// DON'T: Pokemon exception handling (catch 'em all)
try { ... }
catch (Exception) { return null; }  // What went wrong? Nobody knows.

// DON'T: Log and throw (duplicate logging)
catch (Exception ex)
{
    _logger.LogError(ex, "Failed");
    throw;  // Global handler will log AGAIN
}

// DON'T: Expose internals
catch (NpgsqlException ex)
{
    return BadRequest(ex.Message);  // Leaks DB schema info
}

// DON'T: Empty catch
catch (Exception) { }  // Silently swallowed. Debugging nightmare.

// DO: Handle specifically or let it bubble
catch (DuplicateKeyException)
{
    throw new ConflictException("A course with this title already exists");
}
```
