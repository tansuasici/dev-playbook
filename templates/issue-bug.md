# Bug Issue Template

Copy this when reporting a bug on GitHub.

---

**Title**: `bug: <short description of the problem>`

**Labels**: `bug`, `P1-high` (adjust priority)

**Body**:

```markdown
## Bug Description

<!-- Clear, concise description of what's wrong -->

## Steps to Reproduce

1. Go to '...'
2. Click on '...'
3. Enter '...'
4. See error

## Expected Behavior

<!-- What should happen -->

## Actual Behavior

<!-- What actually happens. Include error messages, screenshots, logs if available. -->

## Environment

- **Browser/Client**: Chrome 120 / Safari 18 / API client
- **OS**: macOS / Windows / Linux
- **Deployed version**: commit hash or tag
- **Tenant**: (if multi-tenant)

## Possible Cause

<!-- Optional: If you have a theory about what's causing this -->

## Workaround

<!-- Optional: Is there a temporary workaround? -->
```

---

## GitHub CLI Example

```bash
gh issue create \
  --repo <org>/<repo> \
  --title "bug: login redirect fails with query params in callback URL" \
  --label "bug,P1-high" \
  --body "$(cat <<'EOF'
## Bug Description

When a user is redirected to login from a page with query parameters, the callback URL is truncated after the `?`, causing a 404 after successful login.

## Steps to Reproduce

1. Visit `/courses?filter=active&sort=date` while logged out
2. Get redirected to `/login?callbackUrl=/courses?filter=active&sort=date`
3. Login successfully
4. Redirected to `/courses?filter=active` (truncated)

## Expected Behavior

Redirect to the full original URL including all query parameters.

## Actual Behavior

URL is truncated at the second `?`. Only the first query parameter is preserved.

## Environment

- Browser: Chrome 120
- OS: macOS 15
- Version: commit abc1234

## Possible Cause

The callback URL is not being URL-encoded before being added as a query parameter.
EOF
)"
```
