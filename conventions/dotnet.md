# .NET Conventions

## Architecture: Clean Architecture

```text
Solution/
├── src/
│   ├── Domain/           ← Entities, value objects, enums, domain events
│   ├── Application/      ← Use cases, CQRS handlers, DTOs, interfaces
│   ├── Infrastructure/   ← EF Core, external services, repositories
│   └── Api/              ← Controllers, middleware, DI configuration
└── tests/
    ├── UnitTests/
    └── IntegrationTests/
```

**Dependency rule**: Inner layers never reference outer layers. Domain has zero dependencies.

## Patterns

| Pattern | Tool | Notes |
|---------|------|-------|
| CQRS | MediatR | Separate Commands and Queries |
| Validation | FluentValidation | On every request DTO |
| Object Mapping | Mapster | NOT AutoMapper (commercial license) |
| ORM | EF Core | Repository pattern on top |
| Logging | Serilog | Structured logging to Seq |
| Background Jobs | Hangfire | For long-running tasks |
| Health Checks | AspNetCore.HealthChecks | For every external dependency |
| OpenAPI | Microsoft.AspNetCore.OpenApi + Scalar | NOT Swashbuckle (deprecated) |

## Naming

| Element | Convention | Example |
|---------|------------|---------|
| Public members | PascalCase | `GetCourseById()` |
| Private fields | _camelCase | `_courseRepository` |
| Parameters | camelCase | `courseId` |
| Interfaces | I-prefix | `ICourseRepository` |
| Async methods | Async suffix | `GetCourseByIdAsync()` |
| Constants | PascalCase | `MaxRetryCount` |
| Files | Match class name | `CourseService.cs` |

## Async Rules

- **Async all the way down** — No `.Result`, no `.Wait()`, no `Task.Run()` for IO
- **Always pass `CancellationToken`** in async method signatures
- **Use `ConfigureAwait(false)`** in library code only, not in ASP.NET controllers

## CQRS Structure

```text
Application/
├── Features/
│   └── Courses/
│       ├── Commands/
│       │   ├── CreateCourse/
│       │   │   ├── CreateCourseCommand.cs
│       │   │   ├── CreateCourseCommandHandler.cs
│       │   │   └── CreateCourseCommandValidator.cs
│       │   └── UpdateCourse/
│       │       └── ...
│       └── Queries/
│           └── GetCourseById/
│               ├── GetCourseByIdQuery.cs
│               ├── GetCourseByIdQueryHandler.cs
│               └── CourseDto.cs
```

## Error Handling

- Use Result pattern or custom exceptions — not bare `try/catch` everywhere
- Domain exceptions: `DomainException`, `NotFoundException`, `ConflictException`
- Global exception handler middleware maps exceptions to HTTP status codes
- Never expose stack traces or internal details in API responses

## EF Core

- Fluent API configuration in separate `EntityTypeConfiguration` classes
- Always use migrations, never `EnsureCreated()`
- Use `AsNoTracking()` for read-only queries
- Avoid lazy loading — use explicit `Include()` or projection

## Testing

- **xUnit** for test framework
- **NSubstitute** or **Moq** for mocking
- **TestContainers** for integration tests (real PostgreSQL, Redis)
- Test the service/handler layer, not the framework
- Naming: `MethodName_StateUnderTest_ExpectedBehavior`

```csharp
[Fact]
public async Task CreateCourse_WithValidData_ReturnsCourseId()
```
