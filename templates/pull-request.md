# Pull Request Template

Copy this into `.github/PULL_REQUEST_TEMPLATE.md` in each repo, or use it manually.

---

```markdown
## Summary

- <!-- What does this PR do and why? 1-3 bullet points -->

## Changes

- <!-- List significant changes. Not a file-by-file diff, but what matters. -->

## Test Plan

- [ ] Unit tests added/updated
- [ ] Manual testing completed
- [ ] Edge cases considered
- [ ] CI passes

## Self-Review Checklist

- [ ] I've read every line of the diff
- [ ] No debug code or console.logs left behind
- [ ] No hardcoded values that should be config
- [ ] No security issues (injection, exposed secrets, missing auth)
- [ ] Error handling is appropriate
- [ ] This PR does ONE thing

## Related

- Closes #<!-- issue number -->

---
Generated with [Claude Code](https://claude.com/claude-code)
```

---

## Usage

### Option A: Auto-populate for all PRs

Save the content between the ``` fences as `.github/PULL_REQUEST_TEMPLATE.md` in your repo. GitHub will auto-fill this template every time someone opens a PR.

### Option B: Use with gh CLI

```bash
gh pr create \
  --title "feat: add course enrollment limit" \
  --body "$(cat <<'EOF'
## Summary

- Add MaxCapacity field to courses with enrollment validation

## Changes

- Added `MaxCapacity` column to Course entity + migration
- Enrollment endpoint now returns 409 when course is full
- Course detail page shows remaining spots

## Test Plan

- [x] Unit tests for EnrollStudentCommandHandler capacity check
- [x] Integration test for 409 response
- [x] Manual testing with capacity = 1, 0, null (unlimited)

## Self-Review Checklist

- [x] Read the diff
- [x] No debug code
- [x] No hardcoded values
- [x] No security issues
- [x] Error handling appropriate
- [x] Single concern

## Related

- Closes #42

---
Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```
