# Documentation Standards

## What to Document

| Type | Where | When |
|------|-------|------|
| **API Reference** | OpenAPI/Swagger (auto-generated) | Always for public APIs |
| **Architecture Decisions** | `docs/adr/` or [architecture/decisions.md](../architecture/decisions.md) | When making significant technical choices |
| **README** | Root of every repo | Always |
| **Inline Comments** | In the code | Only when the "why" isn't obvious |
| **Runbooks** | Wiki or `docs/runbooks/` | For operational procedures |
| **CLAUDE.md** | Root of every repo | For AI-assisted development |

## README Template

Every project README should include:

```markdown
# Project Name

One-line description of what this project does.

## Quick Start

### Prerequisites
- Node.js 20+
- Docker & Docker Compose
- PostgreSQL 16 (via Docker)

### Setup
\`\`\`bash
git clone <repo-url>
cd <project>
cp .env.example .env
docker compose up -d
npm install
npm run dev
\`\`\`

### Running Tests
\`\`\`bash
npm test              # unit tests
npm run test:e2e      # end-to-end tests
\`\`\`

## Project Structure

\`\`\`
src/
├── modules/     ← Feature modules
├── shared/      ← Shared utilities
└── config/      ← Configuration
\`\`\`

## API Documentation

Available at `/swagger` when running locally.

## Deployment

See [CI/CD pipeline](.github/workflows/ci.yml) for automated deployment.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

[MIT](LICENSE)
```

## API Documentation

### OpenAPI/Swagger

- **Auto-generate** from code annotations — don't maintain a separate spec file
- **Include examples** for request/response bodies
- **Document all error responses**, not just the happy path
- **Group endpoints** by resource or feature

```csharp
// .NET — Swagger annotations
[HttpPost]
[ProducesResponseType(typeof(CourseDto), StatusCodes.Status201Created)]
[ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
[ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status409Conflict)]
public async Task<IActionResult> CreateCourse([FromBody] CreateCourseRequest request)
```

```typescript
// Next.js / Express — JSDoc or zod-to-openapi
/**
 * @openapi
 * /api/courses:
 *   post:
 *     summary: Create a new course
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             $ref: '#/components/schemas/CreateCourseRequest'
 *     responses:
 *       201:
 *         description: Course created successfully
 *       400:
 *         description: Validation error
 */
```

```python
# Python — FastAPI auto-generates OpenAPI
@router.post("/courses", response_model=CourseResponse, status_code=201)
async def create_course(request: CreateCourseRequest):
    """Create a new course.

    Raises:
        HTTPException(400): Invalid input
        HTTPException(409): Duplicate course title
    """
```

## Inline Comments

### When to Comment

- **Why**, not what — the code already says what it does
- **Business rules** that aren't obvious from code
- **Workarounds** with links to issues or tickets
- **Performance decisions** that look wrong but are intentional
- **Regex patterns** — always explain what they match

### When NOT to Comment

- Obvious code (`i++ // increment i`)
- Function names that are self-explanatory
- TODO/FIXME without issue references
- Commented-out code — delete it, git has history

### Examples

```csharp
// GOOD — explains WHY
// Tenant isolation: RLS handles filtering, but we double-check here
// because admin endpoints bypass RLS for cross-tenant reports
if (!user.IsAdmin && course.TenantId != user.TenantId)
    throw new ForbiddenException();

// GOOD — explains business rule
// Students can unenroll within 14 days of enrollment (refund policy)
if (enrollment.CreatedAt.AddDays(14) < DateTime.UtcNow)
    throw new BusinessRuleException("Unenrollment period has expired");

// BAD — states the obvious
// Check if user is null
if (user == null) ...

// BAD — no issue reference
// TODO: fix this later
```

## CLAUDE.md

Every project should have a `CLAUDE.md` at the root for AI-assisted development.

See [templates/claude-md-starter.md](../templates/claude-md-starter.md) for the starter template.

### Must Include

- Project description and tech stack
- How to build, test, and run
- Key architecture decisions
- Link to this playbook for standards
- Project-specific conventions not covered by the playbook

## Changelogs

### For Libraries / Packages

Follow [Keep a Changelog](https://keepachangelog.com/) format:

```markdown
## [1.2.0] - 2025-03-15

### Added
- Course capacity limits

### Fixed
- Login redirect with query parameters
```

### For Applications

Use **release notes** tied to version tags instead of maintaining a CHANGELOG file. The git history and PR descriptions are the changelog.

## Anti-Patterns

- **No README** — Every project needs one, even internal tools
- **Stale documentation** — Outdated docs are worse than no docs. Keep it updated or delete it.
- **Documentation in a separate system** — Keep docs close to code (same repo) when possible
- **Over-documenting internals** — Code should be self-explanatory. Document interfaces, not implementations.
- **Screenshots without context** — Always explain what the screenshot shows and when it was taken
