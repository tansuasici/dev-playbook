# Code Review Guide

## Why Review?

Even solo developers benefit from review. It forces you to:

- Re-read your own code with fresh eyes
- Catch bugs before they reach main
- Maintain consistent quality standards
- Create a decision trail for future reference

## What to Look For

### Correctness

- Does the code do what the PR says it does?
- Are edge cases handled? (null, empty, zero, negative, max values)
- Is the error handling appropriate? (not too much, not too little)
- Does it work for all tenants / roles / locales?

### Security

- User input validated at the boundary?
- No SQL injection, XSS, or path traversal risks?
- Authentication / authorization checks in place?
- No secrets or credentials in the code?
- Multi-tenant isolation preserved? (TenantId filtering)

### Design

- Is this the simplest solution that works?
- Does it follow the project's architecture? (Clean Architecture layers, CQRS, etc.)
- Is the code in the right place? (not business logic in controllers, not DB calls in domain)
- Any unnecessary abstraction or over-engineering?

### Readability

- Can you understand the code without the PR description?
- Are names descriptive? (variables, functions, classes)
- Is the code self-documenting? (comments only where "why" isn't obvious)
- Consistent with the project's conventions?

### Performance (only when relevant)

- N+1 query problems?
- Missing database indexes for new queries?
- Unnecessary memory allocations in hot paths?
- Missing pagination on list endpoints?

### Testing

- Are there tests for the new behavior?
- Do the tests actually verify the right thing? (not just "test exists")
- Are edge cases tested?
- Are the tests readable and maintainable?

## How to Give Feedback

### Comment Types

Use prefixes to clarify intent:

| Prefix | Meaning | Blocks Merge? |
|--------|---------|---------------|
| **blocker:** | Must fix before merge | Yes |
| **suggestion:** | Consider this, but your call | No |
| **question:** | I don't understand this — explain? | Depends |
| **nit:** | Trivial style/preference issue | No |
| **praise:** | This is well done | No |

### Examples

```text
blocker: This endpoint has no authorization check. Any authenticated
user can delete any course regardless of role.

suggestion: Consider extracting this validation logic into a shared
validator — CourseValidator already handles similar checks.

question: Why are we caching this for 24 hours? Course data changes
frequently. Is there an invalidation strategy?

nit: This variable could be named `remainingCapacity` instead of `rc`
for clarity.

praise: Clean separation of the query logic here. The projection
approach avoids loading the entire entity — nice.
```

### Rules for Reviewers

1. **Be specific** — "This is wrong" is useless. "This misses the case where courseId is null" is helpful.
2. **Explain why** — Don't just say "change this." Say why it matters.
3. **Suggest alternatives** — If you reject an approach, propose another.
4. **Separate must-fix from nice-to-have** — Use the prefixes above.
5. **Acknowledge good work** — Don't only comment on problems.
6. **Review the PR, not the person** — "This code doesn't handle X" not "You forgot X."
7. **Be timely** — Review within 24 hours. Stale PRs kill momentum.

### Rules for Authors

1. **Don't take it personally** — Review feedback is about the code.
2. **Respond to every comment** — Even if just "Done" or "Agreed, fixed."
3. **Explain your reasoning** — If you disagree, explain why with context.
4. **Keep the PR small** — Large PRs get superficial reviews. Small PRs get thorough ones.
5. **Self-review first** — Read your own diff before anyone else does.

## AI-Assisted Review

When using AI (Claude Code) as a reviewer:

- AI reviews catch: style issues, missing error handling, security patterns, convention violations
- AI reviews miss: business logic correctness, UX implications, performance in context
- **AI review supplements human review — it doesn't replace it**
- Always verify AI suggestions against the project's actual requirements
