# Architecture Decision Records

## How to Use This File

When making a significant technical decision, document it here. Future-you (and AI assistants) will thank you.

## Format

```markdown
### ADR-NNN: Title

**Date**: YYYY-MM-DD
**Status**: Accepted | Superseded | Deprecated
**Context**: Why did this decision come up?
**Decision**: What did you decide?
**Consequences**: What are the trade-offs?
```

---

### ADR-001: Clean Architecture for Backend

**Date**: Project start
**Status**: Accepted
**Context**: Need a maintainable, testable backend structure that separates concerns.
**Decision**: Use Clean Architecture with 4 layers: Domain → Application → Infrastructure → Api.
**Consequences**:
- (+) Domain logic is framework-independent and highly testable
- (+) Infrastructure can be swapped (e.g., change ORM, change DB)
- (-) More files and indirection for simple CRUD operations
- (-) New developers need to understand the layer boundaries

### ADR-002: CQRS with MediatR

**Date**: Project start
**Status**: Accepted
**Context**: Need clear separation between read and write operations for maintainability.
**Decision**: Use MediatR for command/query dispatch. One handler per use case.
**Consequences**:
- (+) Each use case is isolated and testable
- (+) Easy to add cross-cutting concerns (validation, logging) via behaviors
- (-) Boilerplate: Command + Handler + Validator per operation
- (-) Overkill for simple CRUD, but consistency wins

### ADR-003: Mapster over AutoMapper

**Date**: Project start
**Status**: Accepted
**Context**: Need object mapping between DTOs and entities.
**Decision**: Use Mapster instead of AutoMapper.
**Consequences**:
- (+) No commercial license issues (AutoMapper changed to commercial)
- (+) Better performance (compile-time mapping)
- (+) Similar API, easy to learn
- (-) Smaller community than AutoMapper

---

## Template for New Decisions

Copy this when adding a new ADR:

```markdown
### ADR-NNN: Title

**Date**: YYYY-MM-DD
**Status**: Accepted
**Context**: <Why did this come up?>
**Decision**: <What was decided?>
**Consequences**:
- (+) <Positive outcome>
- (-) <Trade-off or downside>
```

## Categories of Decisions to Document

- Programming language or framework choices
- Database selections (relational, vector, cache, analytics)
- Authentication/authorization approach
- API design (REST, GraphQL, gRPC)
- AI/ML service provider choices
- Hosting and deployment strategy
- Third-party service integrations
- Library choices where multiple options exist
