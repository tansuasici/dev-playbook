# Feature Issue Template

Copy this when creating a feature issue on GitHub.

---

**Title**: `feat: <short description>`

**Labels**: `enhancement`, `P1-high` (adjust priority)

**Body**:

```markdown
## Context

<!-- Why is this needed? What problem does it solve? -->

## Requirements

- [ ] Requirement 1
- [ ] Requirement 2
- [ ] Requirement 3

## Acceptance Criteria

<!-- How do we know this is done? -->

- [ ] Criteria 1
- [ ] Criteria 2
- [ ] Tests written and passing

## Technical Notes

<!-- Optional: implementation hints, API contracts, related issues -->

## Out of Scope

<!-- What this issue does NOT cover -->
```

---

## GitHub CLI Example

```bash
gh issue create \
  --repo <org>/<repo> \
  --title "feat: add course enrollment capacity limit" \
  --label "enhancement,P1-high" \
  --body "$(cat <<'EOF'
## Context

Instructors need to limit how many students can enroll in a course.

## Requirements

- [ ] Add `MaxCapacity` field to Course entity
- [ ] Validate enrollment count before allowing new enrollment
- [ ] Show remaining capacity on course detail page
- [ ] Return 409 Conflict when course is full

## Acceptance Criteria

- [ ] Enrollment fails gracefully when course is at capacity
- [ ] Capacity is displayed in the UI
- [ ] Unit tests for enrollment validation
- [ ] Integration test for the enrollment endpoint

## Technical Notes

- Requires migration to add `MaxCapacity` column
- Default value: null (unlimited)
EOF
)"
```
