# Dependency Management

## Core Principle

**Dependencies are liabilities, not assets.** Every dependency you add is code you don't control. Add them deliberately, update them regularly, remove them when unused.

## Adding a Dependency

Before adding a new package, ask:

1. **Do I actually need this?** Can I write 20 lines instead of adding a library?
2. **Is it maintained?** Check: last commit date, open issues, release frequency
3. **Is it widely used?** Downloads/stars matter — more users = more eyes on bugs
4. **License compatible?** MIT, Apache 2.0 = safe. GPL = careful. Commercial = check cost.
5. **How big is it?** Don't add a 2MB library for one utility function

### Red Flags

- No commits in 12+ months
- Many open issues with no maintainer response
- Single maintainer with no succession plan
- Transitive dependency tree is huge
- License changed recently (e.g., AutoMapper went commercial)

## Version Pinning

| Context | Strategy | Example |
|---------|----------|---------|
| Application (deployed) | Pin exact versions | `"express": "4.18.2"` |
| Lock files | Always commit | `package-lock.json`, `*.csproj` |
| CI/CD actions | Pin major version | `actions/checkout@v4` |
| Docker base images | Pin specific tag | `node:22.5-alpine` |
| Development tools | Range OK | `"eslint": "^8.0.0"` |

## Updating Dependencies

### Schedule

- **Security patches**: Immediately (same day)
- **Patch updates**: Weekly or bi-weekly
- **Minor updates**: Monthly
- **Major updates**: Plan and test — don't rush

### Process

```bash
# Check for outdated packages
npm outdated                    # Node.js
dotnet list package --outdated  # .NET
pip list --outdated             # Python

# Check for vulnerabilities
npm audit                              # Node.js
dotnet list package --vulnerable       # .NET
pip-audit                              # Python
```

1. Update in a dedicated branch: `chore/update-dependencies`
2. Run full test suite after updating
3. Check for breaking changes in changelogs
4. Don't bundle dependency updates with feature work

### Major Version Upgrades

Major upgrades (e.g., Next.js 15 → 16, .NET 9 → 10) are projects, not chores:

1. Read the migration guide
2. Create a GitHub issue tracking the upgrade
3. Do it in a dedicated branch with focused commits
4. Test thoroughly — major versions often have breaking changes
5. Update CI/CD if SDK versions changed

## Security Vulnerabilities

### Automated Scanning

- Enable **Dependabot** or **Renovate** on GitHub repos
- Run `npm audit` / `dotnet list package --vulnerable` in CI
- Don't ignore vulnerability warnings — they don't go away

### Response

| Severity | Action |
|----------|--------|
| Critical/High | Update within 24 hours. If no fix available, evaluate workaround or alternative package. |
| Medium | Update within 1 week |
| Low | Update in next regular maintenance cycle |

## Removing Dependencies

Unused dependencies are dead weight:

- Increase build time
- Expand attack surface
- Create confusion ("why is this here?")

Periodically audit:

```bash
# Find unused packages (Node.js)
npx depcheck

# .NET — check for unused package references manually or with IDE tools
```

If a package is unused, remove it. Don't leave it "just in case."
