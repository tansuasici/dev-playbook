# Security Audit Checklist

Run this checklist periodically (quarterly) or before major releases.

## Authentication & Authorization

- [ ] Passwords hashed with bcrypt/Argon2 (not MD5/SHA)
- [ ] Access tokens are short-lived (< 30 minutes)
- [ ] Refresh tokens stored in HttpOnly cookies
- [ ] Failed login attempts are rate-limited
- [ ] Session tokens invalidated on logout
- [ ] Role/permission checks on every protected endpoint
- [ ] No privilege escalation paths (user can't elevate to admin)
- [ ] Token refresh endpoint validates the refresh token properly

## Input Validation

- [ ] All user input validated at the API boundary
- [ ] No raw SQL concatenation (parameterized queries only)
- [ ] File uploads validated: MIME type, extension, size limit
- [ ] No path traversal possible via user input
- [ ] HTML output sanitized to prevent XSS
- [ ] JSON parsing rejects unexpected fields (strict deserialization)
- [ ] Query parameters validated (pagination limits, sort fields)

## Data Protection

- [ ] Sensitive data encrypted at rest (database encryption)
- [ ] TLS/HTTPS enforced on all endpoints
- [ ] No secrets in source code or git history
- [ ] `.env` files in `.gitignore`
- [ ] API keys and credentials stored in secrets manager
- [ ] Personal data deletable (GDPR right to erasure)
- [ ] Logs don't contain passwords, tokens, or PII
- [ ] Database backups encrypted

## API Security

- [ ] CORS restricted to known origins (no wildcard in production)
- [ ] Rate limiting enabled on all endpoints
- [ ] Security headers set (X-Content-Type-Options, X-Frame-Options, HSTS, CSP)
- [ ] Error responses don't leak internal details (no stack traces)
- [ ] API versioning doesn't expose deprecated/vulnerable endpoints
- [ ] Request size limits configured (prevent large payload attacks)

## Multi-Tenancy (if applicable)

- [ ] Every database query filters by TenantId
- [ ] Global query filters active and tested
- [ ] Cross-tenant data access is impossible via API
- [ ] Cache keys are tenant-scoped
- [ ] File storage is tenant-isolated
- [ ] Admin endpoints are properly restricted

## Dependencies

- [ ] No known vulnerabilities in dependencies (`npm audit`, `dotnet list package --vulnerable`)
- [ ] Dependencies are up to date (no EOL frameworks/runtimes)
- [ ] Lock files committed and reviewed
- [ ] No unnecessary dependencies (attack surface reduction)

## Infrastructure

- [ ] Database not publicly accessible (firewall rules)
- [ ] SSH keys rotated (no shared keys)
- [ ] Docker containers run as non-root
- [ ] Unused ports closed
- [ ] Health check endpoints don't leak system info
- [ ] Monitoring alerts configured for anomalies

## Code Review

- [ ] Security-sensitive changes reviewed by someone else (or thorough self-review)
- [ ] No hardcoded credentials found (`grep -r "password\|secret\|api_key"`)
- [ ] No TODO/FIXME items related to security
