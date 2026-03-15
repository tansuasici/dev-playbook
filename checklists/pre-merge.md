# Pre-Merge Checklist

Review before merging any PR to `main`.

## Code Quality

- [ ] CI pipeline is green (all checks pass)
- [ ] No linting warnings or errors
- [ ] No type errors
- [ ] Code compiles/builds without warnings

## Self-Review

- [ ] I've read every line of the diff in the PR
- [ ] No debug code left (console.log, print, debugger, TODO/FIXME)
- [ ] No hardcoded values that should be environment variables
- [ ] No commented-out code blocks
- [ ] Variable and function names are clear and descriptive

## Testing

- [ ] Unit tests added for new logic
- [ ] Existing tests still pass
- [ ] Edge cases considered (null, empty, boundary values)
- [ ] Manual testing done for UI changes

## Security

- [ ] No secrets or credentials in the code
- [ ] User input is validated at the boundary
- [ ] No SQL injection risk (parameterized queries)
- [ ] No XSS risk (output is sanitized/escaped)
- [ ] Authentication/authorization checks in place for new endpoints
- [ ] Multi-tenant: TenantId filter applied on all data queries

## API Changes (if applicable)

- [ ] Backward compatible (or breaking change is documented)
- [ ] Request/response DTOs validated
- [ ] Error responses are consistent and informative
- [ ] OpenAPI/Swagger docs updated

## Database Changes (if applicable)

- [ ] Migration created and tested
- [ ] Migration is reversible (down migration works)
- [ ] New columns have appropriate defaults or are nullable
- [ ] Indexes added for frequently queried columns
- [ ] TenantId included on new tables

## PR Metadata

- [ ] PR references the issue (`Closes #N`)
- [ ] PR title follows Conventional Commits format
- [ ] PR description has Summary, Changes, and Test Plan sections
- [ ] PR is focused on ONE concern (not bundled changes)
