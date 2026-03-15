# New Project Setup Checklist

Run through this when starting any new project.

## Repository Setup

- [ ] Create GitHub repo (org or personal)
- [ ] Clone locally
- [ ] Add `.gitignore` (use GitHub's template for your language)
- [ ] Create `CLAUDE.md` from [templates/claude-md-starter.md](../templates/claude-md-starter.md)
- [ ] Create `README.md` with project overview and setup instructions

## GitHub Configuration

- [ ] Create GitHub Project board (Kanban: To Do → In Progress → Testing → Done)
- [ ] Add labels to repo (see [labels.yml](../labels.yml)):
  ```bash
  # Priority labels
  gh label create "P0-critical" --color "B60205" --description "Critical priority" --repo <repo>
  gh label create "P1-high" --color "D93F0B" --description "High priority" --repo <repo>
  gh label create "P2-medium" --color "FBCA04" --description "Medium priority" --repo <repo>
  gh label create "P3-low" --color "0E8A16" --description "Low priority" --repo <repo>
  ```
- [ ] Add PR template: `.github/PULL_REQUEST_TEMPLATE.md` (from [templates/pull-request.md](../templates/pull-request.md))
- [ ] Add issue templates: `.github/ISSUE_TEMPLATE/` (from [templates/](../templates/))
- [ ] Enable branch protection on `main` (if on paid plan):
  - Require PR before merging
  - Require CI status checks to pass
  - Do not allow force pushes

## CI/CD

- [ ] Create `.github/workflows/ci.yml` (follow [process/ci-cd.md](../process/ci-cd.md))
- [ ] Verify CI runs on push and PR
- [ ] Ensure tests block the pipeline (no `continue-on-error`)
- [ ] Add GitHub Secrets for any required environment variables

## Development Environment

- [ ] `docker-compose.yml` for local services (DB, cache, etc.)
- [ ] `.env.example` with all required env vars (no real values)
- [ ] Verify: `git clone` → install → run → tests pass (fresh machine test)
- [ ] Document setup steps in README or CLAUDE.md

## Code Quality

- [ ] Linter configured and running in CI
- [ ] Formatter configured (or format check in CI)
- [ ] Type checking enabled (TypeScript strict, mypy, etc.)
- [ ] Pre-commit hooks (optional but recommended): lint + format on commit

## First Issue

- [ ] Create the first issue on the board (project scaffold or first feature)
- [ ] Assign priority label
- [ ] Add to the project board
- [ ] Start working using the branch → PR → merge flow from day one
