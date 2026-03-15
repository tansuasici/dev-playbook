# Security Standards

## Authentication

### JWT Strategy
- **Access token**: Short-lived (15 minutes)
- **Refresh token**: Long-lived (7 days), stored in HttpOnly cookie
- **Never** store tokens in localStorage (XSS vulnerable)
- Include `tenantId` and `role` in token claims

### Password Policy
- Minimum 8 characters
- At least one uppercase, one lowercase, one digit
- Use bcrypt or Argon2 for hashing (never MD5, never SHA without salt)
- Rate limit login attempts (5 attempts per 15 minutes per IP)

## Authorization

- Role-based access control (RBAC) as the baseline
- Validate permissions at the API layer (middleware or attribute-based)
- Never trust client-side role checks alone — always verify server-side
- Tenant isolation is an authorization boundary — crossing it is a security incident

## Input Validation

### API Layer
- Validate ALL input at the boundary (controllers/route handlers)
- Use schema validation (FluentValidation, Zod, Pydantic)
- Reject invalid input early — don't let it reach business logic

### Common Attacks to Prevent

| Attack | Prevention |
|--------|-----------|
| **SQL Injection** | Parameterized queries (EF Core, prepared statements). Never string-concatenate SQL. |
| **XSS** | Sanitize user-generated content. Use framework defaults (React auto-escapes). CSP headers. |
| **CSRF** | Anti-forgery tokens for form submissions. SameSite cookies. |
| **File Upload** | Validate MIME type AND extension. Size limits. Scan for malware. Store outside webroot. |
| **Mass Assignment** | Use DTOs, never bind directly to entities. Whitelist properties. |
| **Path Traversal** | Never use user input in file paths. Validate and sanitize. |

## API Security

### Headers
```
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'
X-XSS-Protection: 0  (rely on CSP instead)
```

### CORS
- Whitelist only known origins (tenant subdomains)
- Never use `Access-Control-Allow-Origin: *` in production
- Be specific about allowed methods and headers

### Rate Limiting
- Per-tenant and per-user limits
- Stricter limits on auth endpoints
- Return `429 Too Many Requests` with `Retry-After` header

## Secrets Management

- **Never** commit secrets to git (`.env`, API keys, connection strings)
- Use `.env.example` with placeholder values
- Production: environment variables or secrets manager (Azure Key Vault, AWS Secrets Manager)
- Rotate secrets regularly, especially after team member departures

### .gitignore Must Include
```
.env
.env.local
.env.production
*.pem
*.key
credentials.json
appsettings.Development.json  (if it contains real secrets)
```

## Logging

- **Never log**: passwords, tokens, credit card numbers, personal health info
- **OK to log**: user IDs, request paths, response status codes, latency, error types
- Use structured logging (key-value pairs, not interpolated strings)
- Log security events: failed logins, permission denials, cross-tenant access attempts

## Data Privacy (GDPR/KVKK)

- All personal data must be deletable (right to erasure)
- Document what personal data you collect and why
- Provide data export functionality (right to portability)
- Encrypt personal data at rest
- Data retention policies: define how long you keep data and auto-delete after

## Dependency Security

- Run `npm audit` / `dotnet list package --vulnerable` in CI
- Update dependencies with known vulnerabilities promptly
- Pin versions to avoid supply-chain attacks via version hijacking
- Review new dependencies before adding — prefer well-maintained, widely-used packages
