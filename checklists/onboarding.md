# Developer Onboarding Checklist

What to do on your first day with a project (or when onboarding someone new).

## Access & Accounts

- [ ] GitHub organization/repo access granted
- [ ] Added to the project board(s)
- [ ] Access to deployment environments (staging, production)
- [ ] Access to monitoring/logging tools (Seq, Grafana, etc.)
- [ ] Access to communication channels (Slack, Discord, etc.)
- [ ] Secrets/credentials shared securely (never via chat or email)

## Local Environment

- [ ] Clone the repository
- [ ] Read the `README.md` and `CLAUDE.md`
- [ ] Read the [dev-playbook](https://github.com/tansuasici/dev-playbook) — especially `process/` and `conventions/`
- [ ] Install required runtimes and tools:
  - [ ] .NET SDK (check version in `global.json`)
  - [ ] Node.js (check version in `.nvmrc` or `package.json`)
  - [ ] Python (check version in `pyproject.toml`)
  - [ ] Docker Desktop
- [ ] Copy `.env.example` to `.env` and fill in values
- [ ] Start infrastructure: `docker compose up -d`
- [ ] Install dependencies: `dotnet restore` / `npm ci` / `pip install`
- [ ] Run the app locally and verify it starts
- [ ] Run the tests and verify they pass
- [ ] Install git hooks (see [conventions/git-hooks.md](../conventions/git-hooks.md))

## Understand the Project

- [ ] Read the architecture docs in the playbook (`architecture/`)
- [ ] Understand the project structure (where code lives, how layers connect)
- [ ] Review the project board — what's in progress, what's coming
- [ ] Read the last 10 commit messages to understand recent work
- [ ] Read 2-3 recent closed PRs to understand the review process

## First Contribution

- [ ] Pick a small issue from the board (labeled `good first issue` if available)
- [ ] Follow the full workflow:
  1. Move issue to "In Progress"
  2. Create a feature branch
  3. Make the change
  4. Write tests
  5. Open a PR with proper template
  6. Get review, address feedback
  7. Merge and close the issue

## Knowledge Gaps

Don't be afraid to ask. Document answers for the next person:

- [ ] I understand the authentication flow
- [ ] I understand how multi-tenancy works (if applicable)
- [ ] I know how to deploy to staging
- [ ] I know where to find logs when something breaks
- [ ] I know who to ask when I'm stuck
