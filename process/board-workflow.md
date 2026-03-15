# Board Workflow

## Tool: GitHub Projects (Kanban)

Every project has a GitHub Projects board with a standard Kanban layout.

## Columns

```
To Do → In Progress → Testing → Done
```

| Column | Meaning | Who Moves Here |
|--------|---------|----------------|
| **To Do** | Triaged, prioritized, ready to pick up | Product/lead during planning |
| **In Progress** | Actively being worked on | Developer when starting |
| **Testing** | PR open, CI passing, awaiting review/QA | Developer when PR is ready |
| **Done** | Merged, deployed, verified | Auto or developer after merge |

## Rules

1. **Move to In Progress BEFORE writing code** — Not after. This signals ownership.
2. **One In Progress per person** — Focus. Don't juggle 5 issues at once.
3. **In Progress means active work** — If you stop working on it, move it back to To Do.
4. **Testing means PR is ready** — Not "I'll open a PR later." The PR exists and CI is green.
5. **Done means verified** — Not just merged. Confirm it works in the target environment.

## Issue Flow

```
Issue Created
    ↓
[To Do] — Triaged with priority + labels
    ↓
[In Progress] — Developer picks it up, creates branch
    ↓
[Testing] — PR opened with "Closes #N", CI passing
    ↓
[Done] — PR merged, issue closed with completion comment
```

## Board Hygiene

- **Weekly sweep**: Review To Do column, re-prioritize if needed
- **No "No Status" items**: Every issue on the board must be in a column
- **Archive Done items** monthly to keep the board clean
- **If an issue is stale** (no activity for 2+ weeks in To Do): either re-prioritize or close with a reason

## Multi-Repo Projects

When a project spans multiple repos (e.g., API + WebApp):
- Each repo has its own board
- Cross-repo work: create the issue in the primary repo, link to the other
- Sync status manually — if API is done but WebApp isn't, the API issue can be Done while the WebApp issue stays In Progress

## GitHub CLI Quick Reference

```bash
# List project items
gh project item-list <PROJECT_NUMBER> --owner <ORG_OR_USER>

# View specific issue
gh issue view <NUMBER> --repo <ORG>/<REPO>

# Move issue status (requires GraphQL — use the web UI for simplicity)
# Or use gh project item-edit with the field and value IDs
```
