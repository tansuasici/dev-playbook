# Dev Playbook

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/tansuasici/dev-playbook/blob/main/LICENSE)
[![Markdown Lint](https://github.com/tansuasici/dev-playbook/actions/workflows/lint.yml/badge.svg)](https://github.com/tansuasici/dev-playbook/actions/workflows/lint.yml)
[![Version](https://img.shields.io/badge/version-1.0.0-green.svg)](https://github.com/tansuasici/dev-playbook/blob/main/VERSION)

Personal software development playbook. Defines how I build software — from branching strategy to code conventions.

**Purpose**: Every new project reads this repo first and follows these standards. No reinventing the wheel.

## Table of Contents

- [How to Use](#how-to-use)
- [Structure](#structure)
- [Quick Reference](#quick-reference)
- [Principles](#principles)
- [Contributing](#contributing)
- [License](#license)

## How to Use

### For Claude Code / AI Assistants

Add this to your project's `CLAUDE.md`:

```markdown
## Development Process
Follow the playbook at https://github.com/tansuasici/dev-playbook
Read all files under process/ and conventions/ before starting work.
Apply templates/ when creating issues and PRs.
```

Or at the start of a session:

> "Read the dev-playbook repo first, then work on this project."

### For Yourself

Bookmark this repo. Before starting a new project, run through `checklists/new-project-setup.md`.

---

## Structure

```text
dev-playbook/
├── process/                 ← How work flows
│   ├── branching.md             Branch strategy and naming
│   ├── commits.md               Conventional Commits rules
│   ├── issues.md                Issue creation and lifecycle
│   ├── pull-requests.md         PR process and review outcomes
│   ├── board-workflow.md        Kanban board flow
│   ├── ci-cd.md                 CI/CD pipeline standards
│   ├── code-review.md           How to review code
│   ├── incident-response.md     What to do when production breaks
│   └── dependency-management.md Adding, updating, removing deps
├── conventions/             ← How code is written
│   ├── dotnet.md                .NET / C# standards
│   ├── nextjs.md                Next.js / React standards
│   ├── python.md                Python standards
│   ├── naming.md                Naming conventions (all languages)
│   ├── api-design.md            REST API design standards
│   ├── api-error-format.md      Standard error response format (RFC 7807)
│   ├── error-handling.md        Exception handling patterns
│   ├── testing.md               Testing philosophy and practices
│   ├── logging.md               Logging levels, structured logging
│   ├── observability.md         Metrics, tracing, alerting, health checks
│   ├── performance.md           Response budgets, caching, N+1 prevention
│   ├── docker.md                Dockerfile and Compose best practices
│   ├── git-hooks.md             Pre-commit hooks setup
│   ├── environment-management.md  Environment config, feature flags, secrets
│   ├── documentation.md         README template, API docs, inline comments
│   ├── accessibility.md         WCAG 2.1 AA, keyboard nav, screen readers
│   └── i18n.md                  Internationalization and localization
├── architecture/            ← How systems are designed
│   ├── decisions.md             Architecture Decision Records (ADR)
│   ├── multi-tenancy.md         Multi-tenancy with RLS
│   ├── security.md              Security standards and OWASP
│   └── database.md              Schema design, migrations, indexing
├── templates/               ← Copy-paste templates
│   ├── issue-feature.md         Feature request template
│   ├── issue-bug.md             Bug report template
│   ├── pull-request.md          PR template
│   └── claude-md-starter.md     CLAUDE.md starter for new projects
├── checklists/              ← Step-by-step checklists
│   ├── new-project-setup.md     New project bootstrap
│   ├── pre-merge.md             Pre-merge review
│   ├── release.md               Release process
│   ├── security-audit.md        Periodic security audit
│   └── onboarding.md            New developer onboarding
├── .github/                 ← GitHub integration
│   ├── ISSUE_TEMPLATE/          Native issue templates (bug, feature)
│   ├── PULL_REQUEST_TEMPLATE.md Native PR template
│   └── workflows/lint.yml       Markdown lint + broken link check
├── labels.yml               ← Standard GitHub label set
├── CONTRIBUTING.md          ← How to contribute
├── CODE_OF_CONDUCT.md       ← Community standards
├── CHANGELOG.md             ← Version history
├── VERSION                  ← Current version (semver)
└── LICENSE                  ← MIT License
```

## Quick Reference

| I want to... | Read this |
|--------------|-----------|
| Start a new project | [checklists/new-project-setup.md](checklists/new-project-setup.md) |
| Onboard to a project | [checklists/onboarding.md](checklists/onboarding.md) |
| Create a feature branch | [process/branching.md](process/branching.md) |
| Write a commit message | [process/commits.md](process/commits.md) |
| Open an issue | [process/issues.md](process/issues.md) + [templates/](https://github.com/tansuasici/dev-playbook/tree/main/templates) |
| Open a PR | [process/pull-requests.md](process/pull-requests.md) |
| Review code | [process/code-review.md](process/code-review.md) |
| Set up CI/CD | [process/ci-cd.md](process/ci-cd.md) |
| Handle a production incident | [process/incident-response.md](process/incident-response.md) |
| Manage dependencies | [process/dependency-management.md](process/dependency-management.md) |
| Design a REST API | [conventions/api-design.md](conventions/api-design.md) |
| Handle API errors | [conventions/api-error-format.md](conventions/api-error-format.md) |
| Handle exceptions | [conventions/error-handling.md](conventions/error-handling.md) |
| Write .NET code | [conventions/dotnet.md](conventions/dotnet.md) |
| Write Next.js code | [conventions/nextjs.md](conventions/nextjs.md) |
| Write Python code | [conventions/python.md](conventions/python.md) |
| Write tests | [conventions/testing.md](conventions/testing.md) |
| Set up logging | [conventions/logging.md](conventions/logging.md) |
| Set up monitoring | [conventions/observability.md](conventions/observability.md) |
| Optimize performance | [conventions/performance.md](conventions/performance.md) |
| Write a Dockerfile | [conventions/docker.md](conventions/docker.md) |
| Set up git hooks | [conventions/git-hooks.md](conventions/git-hooks.md) |
| Manage environments | [conventions/environment-management.md](conventions/environment-management.md) |
| Write documentation | [conventions/documentation.md](conventions/documentation.md) |
| Make it accessible | [conventions/accessibility.md](conventions/accessibility.md) |
| Add i18n support | [conventions/i18n.md](conventions/i18n.md) |
| Design a database | [architecture/database.md](architecture/database.md) |
| Design a multi-tenant system | [architecture/multi-tenancy.md](architecture/multi-tenancy.md) |
| Review before merge | [checklists/pre-merge.md](checklists/pre-merge.md) |
| Prepare a release | [checklists/release.md](checklists/release.md) |
| Run a security audit | [checklists/security-audit.md](checklists/security-audit.md) |

## Principles

1. **Every change has an issue** — No ad-hoc work. Track everything.
2. **Every issue has a branch** — Never push directly to main.
3. **Every branch has a PR** — Even solo. PRs are your audit trail.
4. **CI must pass before merge** — No exceptions. Tests are not optional.
5. **Keep commits atomic** — One concern per commit. Easy to bisect, easy to revert.
6. **Simplicity over cleverness** — Make it work, make it right, then make it fast.

## Contributing

Contributions are welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

This project is licensed under the [MIT License](https://github.com/tansuasici/dev-playbook/blob/main/LICENSE).
