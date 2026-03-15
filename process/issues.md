# Issue Management

## Core Rule

**Every piece of work starts with an issue.** No ad-hoc changes, no "quick fixes" without tracking.

## Issue Types

### Feature Request
- Label: `enhancement`
- Template: [templates/issue-feature.md](../templates/issue-feature.md)
- Must include: context, requirements checklist, acceptance criteria

### Bug Report
- Label: `bug`
- Template: [templates/issue-bug.md](../templates/issue-bug.md)
- Must include: steps to reproduce, expected vs actual behavior, environment

### Documentation
- Label: `documentation`
- For docs-only changes

## Required Fields

Every issue MUST have:

| Field | Why |
|-------|-----|
| **Title** | Clear, specific (not "fix bug" or "update feature") |
| **Description/Context** | Why this work matters |
| **Checklist** | Concrete tasks as `- [ ]` items |
| **Labels** | At least one type label + one priority label |
| **Priority** | P0/P1/P2/P3 (see below) |

## Priority Labels

| Label | Meaning | Response Time |
|-------|---------|---------------|
| `P0-critical` | System down, data loss, security breach | Drop everything |
| `P1-high` | Major feature broken, blocking work | Next working session |
| `P2-medium` | Important but not blocking | This sprint/week |
| `P3-low` | Nice to have, minor improvement | When time allows |

## Domain Labels (Optional but Recommended)

Add area labels to categorize issues by module:

```
area:auth, area:course, area:analytics, area:ragchat,
area:admin, area:student, area:instructor, area:infra, area:ci
```

## Writing Good Issue Titles

```
# Bad
Fix bug
Update code
Authentication issue
Doesn't work

# Good
fix: Login redirect fails when callback URL contains query params
feat: Add course enrollment capacity limit
bug: ClickHouse analytics query times out for >10K events
docs: Add deployment guide for Kubernetes setup
```

## Issue Lifecycle

```
1. Create issue with proper labels and description
2. Add to project board (auto or manual) → lands in "To Do"
3. When starting work → move to "In Progress", assign yourself
4. Create a branch referencing the issue
5. Work, commit, push, open PR with "closes #N"
6. PR merged → issue auto-closes
7. Add a completion comment (what was done, decisions made)
8. Verify issue moved to "Done" on the board
```

## Completion Comment

When closing an issue, add a comment documenting:

```markdown
## Completed

**Changes:**
- Created `AuthService` with JWT + refresh token logic
- Added `POST /api/auth/login` and `POST /api/auth/refresh` endpoints
- Added integration tests for token lifecycle

**Decisions:**
- Used 15-min access token, 7-day refresh (configurable via appsettings)
- Refresh tokens stored in HttpOnly cookies, not localStorage

**Commits:** abc1234, def5678
```

This creates a searchable history of what was done and why.

## Cross-Repo Issues

When work spans multiple repositories:
- Create the issue in the **primary** repo (where most code changes happen)
- Reference other repos in the body: "Also requires changes in `WebApp` repo"
- Link related issues/PRs from other repos in comments
