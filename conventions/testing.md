# Testing Philosophy

## Core Principle

**Test behavior, not implementation.** Tests should verify what the code does, not how it does it. If you refactor internals and tests break, the tests were wrong.

## Test Pyramid

```
        /  E2E  \          ← Few: critical user flows only
       /  Integr. \        ← Some: API endpoints, DB queries, external services
      /    Unit     \      ← Many: business logic, pure functions, handlers
```

| Level | What to Test | Tools | Speed |
|-------|-------------|-------|-------|
| **Unit** | Business logic, handlers, services, validators, pure functions | xUnit/Vitest/pytest + mocks | Fast (ms) |
| **Integration** | API endpoints, DB operations, external service calls | TestContainers, httpx | Medium (seconds) |
| **E2E** | Critical user flows through the UI | Playwright | Slow (seconds-minutes) |

## What to Test

### Always Test
- Business logic and domain rules
- Validation logic (valid and invalid inputs)
- Edge cases (null, empty, boundary values, max limits)
- Error handling paths
- Authorization rules (can this role do this?)
- Data transformations (mapping, serialization)

### Don't Bother Testing
- Framework boilerplate (DI registration, middleware wiring)
- Simple CRUD with no business logic
- Third-party library internals
- Private methods directly (test them through public API)
- Getters/setters with no logic

## Test Naming

**Pattern**: `MethodName_Scenario_ExpectedResult`

```csharp
// C#
CreateCourse_WithValidData_ReturnsCourseId()
CreateCourse_WithDuplicateTitle_ThrowsConflictException()
EnrollStudent_WhenCourseIsFull_Returns409()
```

```typescript
// TypeScript
describe('CourseService', () => {
  it('creates a course with valid data', () => {})
  it('throws when course title is duplicate', () => {})
  it('returns 409 when course is at capacity', () => {})
})
```

```python
# Python
def test_create_course_with_valid_data_returns_course_id():
def test_create_course_with_duplicate_title_raises_conflict():
def test_enroll_student_when_full_returns_409():
```

## Test Structure: Arrange-Act-Assert

Every test follows this pattern:

```csharp
[Fact]
public async Task EnrollStudent_WhenCourseIsFull_Returns409()
{
    // Arrange — set up the scenario
    var course = CreateCourse(maxCapacity: 1);
    await EnrollStudent(course.Id, studentId: "first-student");

    // Act — execute the thing being tested
    var result = await _handler.Handle(new EnrollStudentCommand(course.Id, "second-student"));

    // Assert — verify the outcome
    result.StatusCode.Should().Be(409);
}
```

Keep each section short. If Arrange is 20 lines, extract a helper or use a builder/factory.

## Mocking Rules

1. **Mock at boundaries** — external services, databases, file system, clock
2. **Don't mock what you own** — if you wrote the class, use the real thing (or an in-memory fake)
3. **Don't mock value objects** — just construct them
4. **One mock per test** (ideally) — if you need 5 mocks, the code has too many dependencies
5. **Integration tests use real infra** — TestContainers for DB, real Redis, etc.

## Test Data

- **Use builders or factories** for complex objects, not raw constructors everywhere
- **Don't share mutable state** between tests — each test sets up its own data
- **Use meaningful test data** — `"john@example.com"` not `"test123"`
- **Avoid magic numbers** — use named constants or clearly labeled variables

## Coverage

- **Don't chase 100%** — diminishing returns after ~80%
- **Cover the critical paths**: auth, payments, data mutations, business rules
- **Uncovered code is a signal**, not a crime — investigate, don't blindly add tests
- **CI should report coverage** but not block on a threshold (quality > quantity)

## When to Write Tests

- **Before fixing a bug**: Write a failing test that reproduces the bug, then fix it
- **During feature development**: Write tests as you implement, not after
- **Not retroactively for old code**: Unless you're modifying it — then add tests for what you touch

## Flaky Tests

Flaky tests (pass sometimes, fail sometimes) are worse than no tests:
- **Fix immediately** or delete — don't leave them failing intermittently
- Common causes: timing issues, shared state, network dependencies, date/time
- Never use `continue-on-error` or `|| true` as a "fix" for flaky tests
