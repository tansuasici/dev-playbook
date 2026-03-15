# Pull Request Process

## Core Rule

**Every change reaches `main` through a PR.** Even solo developers benefit from the audit trail, CI gate, and forced pause to review.

## PR Creation

### Title
- Keep under 70 characters
- Use imperative mood: "Add user auth" not "Added user auth"
- Prefix with type if helpful: `feat: Add user auth`, `fix: Resolve login loop`

### Body Structure

Every PR must have:

```markdown
## Summary
- <1-3 bullet points explaining what this PR does and why>

## Changes
- <List of significant changes, not a file-by-file diff>

## Test Plan
- [ ] Unit tests pass
- [ ] Manual testing done for <scenario>
- [ ] Edge cases considered: <list>

## Related
- Closes #<issue-number>
```

### Linking Issues

Always reference the issue:
- `Closes #42` — auto-closes the issue on merge
- `Fixes #42` — same behavior
- `Related to #42` — links without auto-closing (use for partial work)

## PR Rules

1. **One concern per PR** — Don't mix features, fixes, and refactors
2. **CI must pass** — Never merge with failing checks
3. **Keep PRs small** — Under 400 lines changed is ideal. Over 800 needs a good reason.
4. **No WIP merges** — If it's not ready, mark as Draft PR
5. **Self-review before requesting review** — Read your own diff first
6. **Update the branch** — Rebase or merge main before merging if behind

## Self-Review Checklist (Solo Developer)

Even without a team, review your own PR before merging:

- [ ] I've read every line of the diff
- [ ] No debug code, console.logs, or TODO comments left behind
- [ ] No hardcoded values that should be config
- [ ] Error handling is appropriate (not excessive, not missing)
- [ ] No security issues (SQL injection, XSS, exposed secrets)
- [ ] Tests cover the happy path and at least one edge case
- [ ] The PR does ONE thing (not bundled changes)

## Merge Strategy

- **Squash and merge** for feature branches (clean main history)
- **Merge commit** for large PRs where individual commits tell a story
- **Never rebase and merge** on shared branches (rewrites history)

After merge:
- Delete the feature branch (remote and local)
- Verify the issue was auto-closed
- Check that the board status updated

## Draft PRs

Use Draft PRs when:
- Work is in progress but you want CI feedback
- You want to show the approach before finishing
- You need to save work across machines

Convert to "Ready for review" when done.

## PR Size Guide

| Size | Lines | Expectation |
|------|-------|-------------|
| Small | < 100 | Quick review, merge fast |
| Medium | 100-400 | Normal review cycle |
| Large | 400-800 | Needs justification. Consider splitting. |
| XL | 800+ | Almost always should be split. Exception: generated code, migrations. |
