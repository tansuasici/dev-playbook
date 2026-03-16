# Git Hooks

## Purpose

Git hooks run automated checks before commits and pushes, catching problems before they reach the remote. Think of them as a local CI that runs instantly.

## Recommended Hooks

| Hook | When | What to Run |
|------|------|-------------|
| `pre-commit` | Before each commit | Lint + format staged files |
| `commit-msg` | After writing commit message | Validate Conventional Commits format |
| `pre-push` | Before pushing to remote | Run tests |

## Setup by Stack

### Node.js / Next.js — Husky + lint-staged

```bash
# Install
npm install --save-dev husky lint-staged

# Initialize husky
npx husky init
```

`.husky/pre-commit`:
```bash
npx lint-staged
```

`package.json`:
```json
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md,yml}": ["prettier --write"]
  }
}
```

`.husky/commit-msg`:
```bash
npx --no -- commitlint --edit "$1"
```

Install commitlint:
```bash
npm install --save-dev @commitlint/cli @commitlint/config-conventional
```

`commitlint.config.js`:
```javascript
export default { extends: ['@commitlint/config-conventional'] };
```

### .NET — Husky.Net

```bash
dotnet tool install Husky
dotnet husky install
dotnet husky add pre-commit -c "dotnet format --verify-no-changes"
dotnet husky add pre-commit -c "dotnet build --no-restore"
```

### Python — pre-commit

```bash
pip install pre-commit
```

`.pre-commit-config.yaml`:
```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.5.0
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.10.0
    hooks:
      - id: mypy
```

```bash
pre-commit install
```

## Rules

1. **Hooks should be fast** — Under 5 seconds. If it takes longer, run only on staged files.
2. **Only check staged files** — Don't lint the entire codebase on every commit.
3. **Commit hook config to the repo** — `.husky/`, `.pre-commit-config.yaml` should be versioned.
4. **Document setup** — New developers should know to run the install command.
5. **Never skip hooks** — Don't use `--no-verify` unless you have a very good reason (and you probably don't).

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Hook not running | Run the install command again (`npx husky install`, `pre-commit install`) |
| Hook too slow | Use lint-staged to check only staged files, not the whole repo |
| False positive blocking commit | Fix the issue. Don't skip the hook. |
| Team member doesn't have hooks | Add `prepare` script: `"prepare": "husky"` in package.json |
