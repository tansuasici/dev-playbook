# Contributing to Dev Playbook

First off, thanks for taking the time to contribute! This playbook is meant to be a living document — improvements, corrections, and new guides are always welcome.

## How to Contribute

### Reporting Issues

- Use the **Bug Report** template for errors, broken links, or incorrect information
- Use the **Feature Request** template for suggesting new guides or improvements
- Check existing issues first to avoid duplicates

### Suggesting Changes

1. **Fork** the repository
2. **Create a branch**: `docs/<short-description>` (e.g., `docs/add-graphql-guide`)
3. **Make your changes** following the style guide below
4. **Submit a PR** with a clear description of what you changed and why

### What We're Looking For

- **New guides** for technologies or practices not yet covered
- **Corrections** to existing content (outdated info, broken examples)
- **Better examples** — real-world, practical code snippets
- **Translations** — making the playbook accessible in other languages
- **Typo fixes** — even small improvements matter

## Style Guide

### File Structure

Every guide follows this pattern:

```markdown
# Title

## Section (with table if applicable)

| Column | Column |
|--------|--------|

### Subsection

- Bullet points for rules
- Code blocks for examples

## Anti-Patterns (if applicable)

What NOT to do, with examples.
```

### Writing Style

- **Be direct** — lead with the rule, then explain why
- **Use tables** for comparisons and reference material
- **Include code examples** in all relevant languages (.NET, TypeScript, Python)
- **Show good AND bad** — `// GOOD` and `// BAD` patterns
- **Keep it practical** — real-world examples over theoretical explanations
- **No fluff** — every sentence should add value

### Code Examples

- Use fenced code blocks with language identifiers
- Include examples for .NET (C#), TypeScript, and Python where applicable
- Keep examples minimal but complete — enough to understand the concept
- Use realistic variable names and scenarios

### Naming Conventions

- **Files**: `kebab-case.md` (e.g., `api-design.md`, `error-handling.md`)
- **Headings**: Title Case for H1, Sentence case for H2+
- **Folders**: lowercase, single word when possible

## Review Process

1. All PRs require at least one review
2. Reviewers check for accuracy, clarity, and consistency with existing guides
3. Discussions are welcome — suggest improvements, ask questions
4. Once approved, maintainer merges with squash

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating, you agree to uphold this code.

## Questions?

Open an issue with the label `question` — happy to help.
