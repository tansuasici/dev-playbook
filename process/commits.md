# Commit Conventions

## Format: Conventional Commits

```
<type>: <description>

[optional body]

[optional footer]
```

## Types

| Type | When to Use | Example |
|------|-------------|---------|
| `feat` | New feature or capability | `feat: add user registration flow` |
| `fix` | Bug fix | `fix: resolve null reference in payment service` |
| `docs` | Documentation only | `docs: add API authentication guide` |
| `refactor` | Code change that neither fixes nor adds | `refactor: extract email sending into service` |
| `perf` | Performance improvement | `perf: add database index for user lookups` |
| `test` | Adding or updating tests | `test: add unit tests for OrderService` |
| `chore` | Build, CI, dependencies, tooling | `chore: upgrade to .NET 10` |
| `style` | Formatting, whitespace (no logic change) | `style: fix indentation in auth controller` |
| `ci` | CI/CD changes | `ci: add PostgreSQL service to test pipeline` |
| `revert` | Reverting a previous commit | `revert: feat: add user registration flow` |

## Rules

1. **Description is imperative mood** — "add feature" not "added feature" or "adds feature"
2. **Lowercase first letter** — `feat: add...` not `feat: Add...`
3. **No period at the end** — `feat: add auth` not `feat: add auth.`
4. **Max 72 characters** for the first line
5. **One concern per commit** — Don't mix a feature, a fix, and a refactor in one commit
6. **Language: English** — All commit messages in English, regardless of project language

## Breaking Changes

Use `!` after the type or add `BREAKING CHANGE:` in the footer:

```
feat!: change authentication to OAuth2

BREAKING CHANGE: JWT token format has changed. All existing tokens will be invalidated.
```

## Body (Optional)

Use the body to explain **why**, not what. The diff shows what changed.

```
fix: prevent duplicate enrollment for same course

Students could enroll multiple times by rapidly clicking the enroll button.
Added a unique constraint on (StudentId, CourseId) and a client-side debounce.
```

## Co-Authorship

When working with AI assistants:

```
feat: implement course analytics dashboard

Co-Authored-By: Claude <noreply@anthropic.com>
```

## What NOT to Do

```
# Too vague
fix: fix bug
feat: update code
chore: changes

# Too broad (multiple concerns)
feat: add auth, fix login bug, update deps

# Not imperative
feat: added user registration
feat: adding user registration

# Not English
feat: kullanici kayit eklendi
```

## Commit Frequency

- Commit when you have a logical unit of work complete
- Don't commit broken code to shared branches
- It's fine to have many small commits on a feature branch — they tell the story
- Squash only if the PR has truly noisy WIP commits
