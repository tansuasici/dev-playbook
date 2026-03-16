# Branching Strategy

## Branch Types

| Branch | Pattern | Source | Merges Into | Purpose |
|--------|---------|--------|-------------|---------|
| Main | `main` | — | — | Production-ready code. Always deployable. |
| Feature | `feat/<issue-slug>` | main | main (via PR) | New features |
| Fix | `fix/<issue-slug>` | main | main (via PR) | Bug fixes |
| Hotfix | `hotfix/<issue-slug>` | main | main (via PR) | Critical production fixes |
| Chore | `chore/<slug>` | main | main (via PR) | Dependency updates, CI changes, cleanup |
| Docs | `docs/<slug>` | main | main (via PR) | Documentation changes |
| Refactor | `refactor/<slug>` | main | main (via PR) | Code restructuring without behavior change |

## Rules

1. **Never push directly to `main`** — All changes go through a PR.
2. **One branch per issue** — Branch name should reference the issue or describe the work.
3. **Keep branches short-lived** — Merge within days, not weeks. Long-lived branches cause merge hell.
4. **Delete branches after merge** — No stale branches. Clean up both local and remote.
5. **Pull before branch** — Always create branches from an up-to-date `main`.

## Naming Convention

```text
<type>/<short-descriptive-slug>
```

**Examples**:

- `feat/user-authentication`
- `fix/login-redirect-loop`
- `hotfix/payment-null-reference`
- `chore/upgrade-dotnet-10`
- `docs/api-reference`
- `refactor/extract-email-service`

**Rules**:

- Lowercase only
- Hyphens for word separation (no underscores, no spaces)
- No issue numbers in branch names (the PR links to the issue)
- Keep it under 50 characters

## Workflow

```text
1. Pick an issue from the board
2. Move issue to "In Progress"
3. Pull latest main:     git checkout main && git pull
4. Create branch:        git checkout -b feat/my-feature
5. Do the work, commit often
6. Push:                 git push -u origin feat/my-feature
7. Open PR referencing the issue
8. CI passes → merge
9. Delete branch:        git branch -d feat/my-feature
                         git push origin --delete feat/my-feature
```

## When You Don't Need a Branch

- Truly trivial changes (typo in README, comment fix) can still use a branch, but if the change is < 3 lines and the project is solo, a direct push with a proper commit message is acceptable. **This is the exception, not the rule.**

## Develop Branch?

Not needed for small-to-medium projects with a solo developer or small team. Main-based flow with feature branches is simpler and sufficient. Consider a `develop` branch only when:

- Multiple people work on unrelated features simultaneously
- You need a staging environment that tracks a separate branch
- Release cycles require a stabilization period
