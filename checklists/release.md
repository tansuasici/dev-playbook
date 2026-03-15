# Release Checklist

Run through this before tagging a release.

## Pre-Release

- [ ] All planned issues for this release are Done on the board
- [ ] No open P0 or P1 bugs
- [ ] All CI pipelines are green on `main`
- [ ] Manual smoke test on staging environment
- [ ] Database migrations applied and verified on staging

## Versioning

Use [Semantic Versioning](https://semver.org/):

```
v<MAJOR>.<MINOR>.<PATCH>

MAJOR — Breaking changes (incompatible API changes)
MINOR — New features (backward compatible)
PATCH — Bug fixes (backward compatible)
```

- [ ] Version number determined based on changes since last release
- [ ] CHANGELOG or release notes drafted

## Tag and Release

```bash
# Create annotated tag
git tag -a v1.2.0 -m "Release v1.2.0: <summary>"

# Push tag
git push origin v1.2.0

# Create GitHub release
gh release create v1.2.0 --title "v1.2.0" --notes "$(cat <<'EOF'
## What's New
- feat: Added course enrollment capacity (#42)
- feat: Added analytics dashboard (#45)

## Bug Fixes
- fix: Login redirect with query params (#48)

## Breaking Changes
- None
EOF
)"
```

## Post-Release

- [ ] Verify deployment succeeded (check logs, health endpoint)
- [ ] Smoke test production (critical user flows)
- [ ] Monitor error rates for 30 minutes after deploy
- [ ] Close the milestone (if using milestones)
- [ ] Announce release to stakeholders (if applicable)

## Rollback Plan

If something goes wrong:

1. **Revert the deploy**: Redeploy the previous version (previous Docker image tag)
2. **Don't panic**: Check logs first, understand the failure
3. **Hotfix if small**: If the fix is obvious and small, branch `hotfix/...`, fix, PR, merge, redeploy
4. **Revert if complex**: `git revert <merge-commit>`, PR, merge, redeploy
5. **Post-mortem**: Document what went wrong and how to prevent it
