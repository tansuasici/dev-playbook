# Dev Playbook

Personal software development playbook. Defines how I build software — from branching strategy to code conventions.

**Purpose**: Every new project reads this repo first and follows these standards. No reinventing the wheel.

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

> "Read https://github.com/tansuasici/dev-playbook first, then work on this project."

### For Yourself

Bookmark this repo. Before starting a new project, run through `checklists/new-project-setup.md`.

---

## Structure

```
dev-playbook/
├── process/                 ← How work flows (branch, commit, PR, board)
│   ├── branching.md
│   ├── commits.md
│   ├── issues.md
│   ├── pull-requests.md
│   ├── board-workflow.md
│   └── ci-cd.md
├── conventions/             ← How code is written
│   ├── dotnet.md
│   ├── nextjs.md
│   ├── python.md
│   └── naming.md
├── architecture/            ← How systems are designed
│   ├── decisions.md
│   ├── multi-tenancy.md
│   └── security.md
├── templates/               ← Copy-paste templates
│   ├── issue-feature.md
│   ├── issue-bug.md
│   ├── pull-request.md
│   └── claude-md-starter.md
├── checklists/              ← Step-by-step checklists
│   ├── new-project-setup.md
│   ├── pre-merge.md
│   └── release.md
└── labels.yml               ← Standard GitHub label set
```

## Quick Reference

| I want to... | Read this |
|--------------|-----------|
| Start a new project | [checklists/new-project-setup.md](checklists/new-project-setup.md) |
| Create a feature branch | [process/branching.md](process/branching.md) |
| Write a commit message | [process/commits.md](process/commits.md) |
| Open an issue | [process/issues.md](process/issues.md) + [templates/](templates/) |
| Open a PR | [process/pull-requests.md](process/pull-requests.md) |
| Set up CI/CD | [process/ci-cd.md](process/ci-cd.md) |
| Write .NET code | [conventions/dotnet.md](conventions/dotnet.md) |
| Write Next.js code | [conventions/nextjs.md](conventions/nextjs.md) |
| Write Python code | [conventions/python.md](conventions/python.md) |
| Design a multi-tenant system | [architecture/multi-tenancy.md](architecture/multi-tenancy.md) |
| Review before merge | [checklists/pre-merge.md](checklists/pre-merge.md) |

## Principles

1. **Every change has an issue** — No ad-hoc work. Track everything.
2. **Every issue has a branch** — Never push directly to main.
3. **Every branch has a PR** — Even solo. PRs are your audit trail.
4. **CI must pass before merge** — No exceptions. Tests are not optional.
5. **Keep commits atomic** — One concern per commit. Easy to bisect, easy to revert.
6. **Simplicity over cleverness** — Make it work, make it right, then make it fast.
