# Logging Standards

## Log Levels

| Level | When to Use | Example |
|-------|-------------|---------|
| **Fatal** | Application cannot continue | DB connection permanently lost, corrupt config |
| **Error** | Operation failed, needs attention | Unhandled exception, external service down |
| **Warning** | Something unexpected but handled | Retry succeeded, deprecated API called, rate limit approaching |
| **Information** | Normal business events | User logged in, course created, payment processed |
| **Debug** | Diagnostic detail for development | Request/response payloads, cache hit/miss, query timing |
| **Verbose/Trace** | Extremely detailed, noisy | Method entry/exit, variable values (dev only) |

### Production Minimum Level: `Information`

Debug and Verbose should never be enabled in production by default. Use dynamic log level switching for temporary debugging.

## Structured Logging

**Always use structured logging** — key-value pairs, not string interpolation.

```csharp
// GOOD — structured, searchable
_logger.LogInformation("Course created {CourseId} by {UserId} in tenant {TenantId}",
    courseId, userId, tenantId);

// BAD — string interpolation, not searchable
_logger.LogInformation($"Course created {courseId} by {userId}");
```

```typescript
// GOOD
logger.info('Course created', { courseId, userId, tenantId });

// BAD
logger.info(`Course created ${courseId} by ${userId}`);
```

```python
# GOOD
logger.info("Course created", extra={"course_id": course_id, "user_id": user_id})

# BAD
logger.info(f"Course created {course_id} by {user_id}")
```

## What to Log

### Always Log
- **Authentication events**: login success/failure, token refresh, logout
- **Authorization failures**: forbidden access attempts
- **Business events**: resource created/updated/deleted, state transitions
- **External service calls**: request sent, response received, errors (with latency)
- **Background job lifecycle**: started, completed, failed
- **Application startup/shutdown**: config loaded, services registered, graceful shutdown

### Never Log
- Passwords or password hashes
- Access tokens, refresh tokens, API keys
- Credit card numbers or financial details
- Personal health information
- Full request/response bodies in production (use Debug level only)
- Personal data beyond what's needed (email OK for auth events, not for every log)

### Log with Caution
- Email addresses — OK in auth context, mask elsewhere
- User IDs — OK, they're not PII by themselves
- IP addresses — may be PII under GDPR, log only for security events
- Request bodies — Debug level only, redact sensitive fields

## Context Fields

Include these fields consistently in every log entry:

| Field | Purpose | Example |
|-------|---------|---------|
| `TenantId` | Multi-tenant isolation | `"tenant-abc123"` |
| `UserId` | Who triggered the action | `"user-456"` |
| `CorrelationId` | Trace across services | `"req-789xyz"` |
| `RequestPath` | Which endpoint | `"/api/courses"` |
| `Duration` | How long it took | `145` (ms) |

Use middleware or logging enrichers to add these automatically — don't manually pass them everywhere.

## Log Aggregation

- **Development**: Console output (readable, colored)
- **Staging/Production**: Centralized log aggregation (Seq, ELK, Grafana Loki)
- **Retention**: 30 days for Info+, 7 days for Debug (if enabled)
- **Alerts**: Set up alerts for Error and Fatal level spikes

## Anti-Patterns

```csharp
// DON'T: Log and throw — produces duplicate noise
try { ... }
catch (Exception ex)
{
    _logger.LogError(ex, "Something failed");
    throw; // The global handler will log this AGAIN
}

// DO: Log at the boundary (global exception handler) or at the catch, not both.

// DON'T: Empty catch with log
catch (Exception ex)
{
    _logger.LogError(ex, "Error occurred");
    // and then what? Swallowed silently.
}

// DON'T: Log every method entry/exit in production
_logger.LogInformation("Entering GetCourseById");  // Noise
_logger.LogInformation("Exiting GetCourseById");   // Noise

// DO: Log meaningful events with context
_logger.LogInformation("Course retrieved {CourseId} in {Duration}ms", courseId, elapsed);
```
