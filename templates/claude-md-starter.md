# CLAUDE.md Starter Template

Copy this to the root of a new project and customize.

---

```markdown
# <Project Name> — CLAUDE.md

> <One-line description of the project>

---

## Development Process

Follow the playbook at https://github.com/tansuasici/dev-playbook
Read all files under `process/` and `conventions/` before starting work.

---

## Project Context

<!-- What is this project? Who uses it? What problem does it solve? -->

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | |
| Frontend | |
| Database | |
| Cache | |
| CI/CD | |

---

## Getting Started

```bash
# Clone
git clone <repo-url>
cd <project>

# Install dependencies
<commands>

# Run locally
<commands>

# Run tests
<commands>
```

---

## Project Structure

```
<project>/
├── src/
│   └── ...
├── tests/
│   └── ...
└── ...
```

---

## Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `DATABASE_URL` | PostgreSQL connection string | Yes |
| `REDIS_URL` | Redis connection string | No (defaults to localhost) |

---

## Code Conventions

Follow `conventions/` in the dev-playbook. Project-specific overrides:

- <!-- Any project-specific rules that override or extend the playbook -->

---

## Task Management

<!-- GitHub Projects board URL -->
<!-- Kanban workflow: To Do → In Progress → Testing → Done -->

---

## Infrastructure

| Service | Port | Purpose |
|---------|------|---------|
| | | |

---

## Key Decisions

<!-- Link to ADRs or document project-specific architectural decisions here -->
```
