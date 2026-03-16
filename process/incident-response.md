# Incident Response

## Severity Levels

| Severity | Definition | Response Time | Example |
|----------|-----------|---------------|---------|
| **SEV-1** | System down, all users affected | Immediately | Database crash, main domain unreachable |
| **SEV-2** | Major feature broken, many users affected | Within 1 hour | Login broken, payments failing |
| **SEV-3** | Minor feature broken, workaround exists | Within 24 hours | Export button not working, UI glitch |
| **SEV-4** | Cosmetic or non-urgent issue | Next sprint | Typo on page, alignment off |

## When Something Breaks

### Step 1: Assess (2 minutes)

- What is broken?
- Who is affected? (all users, one tenant, one role?)
- Is data at risk? (data loss, corruption, leak?)
- What changed recently? (last deploy, config change, infrastructure update?)

### Step 2: Communicate (5 minutes)

- Notify stakeholders that you're aware and investigating
- Set expectations: "Investigating, will update in 30 minutes"
- Don't guess at the cause — say what you know

### Step 3: Mitigate (first priority)

**Stop the bleeding before finding root cause.**

Options (in order of preference):

1. **Rollback**: Deploy the previous known-good version
2. **Feature flag**: Disable the broken feature
3. **Hotfix**: If the fix is obvious and small (< 10 lines)
4. **Scale**: If it's a capacity issue, add resources

```bash
# Rollback to previous deployment
docker pull ghcr.io/org/api:previous-tag
docker compose up -d api

# Or revert the last merge
git revert <merge-commit-hash>
# Open PR, get CI green, merge, deploy
```

### Step 4: Fix Root Cause

After the immediate impact is mitigated:

1. Create a GitHub issue labeled `bug` + `P0-critical` or `P1-high`
2. Branch: `hotfix/<description>`
3. Fix, test, PR, review, merge
4. Deploy and verify

### Step 5: Post-Mortem

Write a post-mortem for any SEV-1 or SEV-2 incident. No blame — focus on systems.

## Post-Mortem Template

```markdown
## Incident: <Title>

**Date**: YYYY-MM-DD
**Severity**: SEV-1 / SEV-2
**Duration**: X hours/minutes
**Impact**: <Who was affected and how>

### Timeline

| Time | Event |
|------|-------|
| HH:MM | First alert / user report |
| HH:MM | Investigation started |
| HH:MM | Root cause identified |
| HH:MM | Mitigation applied |
| HH:MM | Confirmed resolved |

### Root Cause

<What actually caused the issue — be specific>

### What Went Well

- <Things that helped resolve the issue faster>

### What Went Wrong

- <Things that made the issue worse or delayed resolution>

### Action Items

- [ ] <Preventive measure 1> — Owner: @who — Due: YYYY-MM-DD
- [ ] <Preventive measure 2> — Owner: @who — Due: YYYY-MM-DD
- [ ] <Monitoring/alerting improvement> — Owner: @who — Due: YYYY-MM-DD
```

## Prevention

- **Monitor**: Set up health checks and alerts before you need them
- **Deploy small**: Small deploys = small blast radius = easy rollback
- **Feature flags**: Gate risky features behind flags
- **Staging first**: Always deploy to staging before production
- **Automated tests**: Catch regressions before they reach production
- **Database backups**: Automated, tested, restorable
