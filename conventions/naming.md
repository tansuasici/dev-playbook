# Naming Conventions

## Universal Rules

1. **English only** — All code, variables, functions, files, commits, branches, PR titles: English
2. **Be descriptive** — `getUserById` not `getUser`, `isEmailVerified` not `flag`
3. **Be consistent** — Pick one term and stick with it. Don't mix `user`/`account`/`member` for the same concept.
4. **No abbreviations** — `repository` not `repo`, `configuration` not `config`. Exception: universally understood ones (`id`, `url`, `api`, `dto`, `db`)

## By Language

| Element | .NET (C#) | TypeScript/JS | Python |
|---------|-----------|---------------|--------|
| Files | PascalCase.cs | kebab-case.ts | snake_case.py |
| Classes | PascalCase | PascalCase | PascalCase |
| Interfaces | IPascalCase | PascalCase | PascalCase (Protocol) |
| Functions | PascalCase | camelCase | snake_case |
| Variables | camelCase | camelCase | snake_case |
| Constants | PascalCase | SCREAMING_SNAKE | SCREAMING_SNAKE |
| Private fields | _camelCase | _camelCase or #field | _snake_case |
| Enums | PascalCase | PascalCase | PascalCase |

## Git

| Element | Convention | Example |
|---------|------------|---------|
| Branch | type/kebab-case | `feat/user-authentication` |
| Commit | type: lowercase description | `feat: add user login` |
| Tag | semver | `v1.0.0` |
| PR title | imperative, < 70 chars | `Add course enrollment capacity` |

## Project Structure

| Element | Convention | Example |
|---------|------------|---------|
| Directories | kebab-case | `user-management/` |
| Config files | kebab-case or dot-notation | `.env`, `docker-compose.yml` |
| Documentation | kebab-case.md | `api-reference.md` |
| Test files | Match source + test suffix | `course-service.test.ts`, `test_course_service.py` |

## Database

| Element | Convention | Example |
|---------|------------|---------|
| Tables | PascalCase (plural) | `Courses`, `EnrollmentRecords` |
| Columns | PascalCase | `CreatedAt`, `TenantId` |
| Foreign keys | FK_Child_Parent | `FK_Enrollment_Course` |
| Indexes | IX_Table_Column | `IX_Courses_TenantId` |
| Migrations | Timestamp_Description | `20250310_AddCourseTable` |

## API Endpoints

```text
GET    /api/courses              ← List
GET    /api/courses/{id}         ← Get by ID
POST   /api/courses              ← Create
PUT    /api/courses/{id}         ← Full update
PATCH  /api/courses/{id}         ← Partial update
DELETE /api/courses/{id}         ← Delete

# Nested resources
GET    /api/courses/{id}/students
POST   /api/courses/{id}/enroll
```

- Plural nouns for resources
- kebab-case for multi-word: `/api/course-categories`
- No verbs in URLs (except actions): `/api/courses/{id}/enroll` is OK
- Version prefix if needed: `/api/v1/courses`

## Common Naming Patterns

| Concept | Pattern | Example |
|---------|---------|---------|
| Boolean | is/has/can/should prefix | `isActive`, `hasPermission` |
| Collections | plural | `courses`, `studentIds` |
| Handlers | verb + noun + Handler | `CreateCourseHandler` |
| Services | noun + Service | `CourseService` |
| Repositories | noun + Repository | `CourseRepository` |
| DTOs | noun + Dto/Request/Response | `CourseDto`, `CreateCourseRequest` |
| Validators | noun + Validator | `CreateCourseValidator` |
| Middleware | noun + Middleware | `TenantMiddleware` |
