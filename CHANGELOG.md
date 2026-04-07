# Changelog

All notable changes to this playbook will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [1.1.0](https://github.com/tansuasici/dev-playbook/compare/v1.0.0...v1.1.0) (2026-04-07)


### Features

* add 12 new guides — code review, API design, testing, logging, docker, incidents, and more ([bd43392](https://github.com/tansuasici/dev-playbook/commit/bd43392e7cb9007b6f9223e1f0a039e63b328218))
* add open-source infrastructure and 7 new convention guides ([c739cfc](https://github.com/tansuasici/dev-playbook/commit/c739cfc65e229dfd5fb8314823c5c05e7734d9da))
* add PR review outcomes flow (approve, comment, decline) ([71fc964](https://github.com/tansuasici/dev-playbook/commit/71fc96428ee47b7ab50cb484ad9d242fb8584102))
* initialize dev-playbook with full development standards ([11adf4b](https://github.com/tansuasici/dev-playbook/commit/11adf4ba49dd4eba9d834fc9621e4ddeddfc7775))


### Bug Fixes

* correct relative path to CONTRIBUTING.md in PR template ([a1a1f72](https://github.com/tansuasici/dev-playbook/commit/a1a1f72e681015f6529c8682082e599b1dd10973))
* replace angle-bracket URL with plain text for MDX compatibility ([8791153](https://github.com/tansuasici/dev-playbook/commit/87911530f9dc43d1bd8b44f9d5681c9696eb2f52))
* resolve 145 markdownlint errors across all docs ([5e4d5a4](https://github.com/tansuasici/dev-playbook/commit/5e4d5a47f06baeba5add68581f2bccd4ab00eb96))
* resolve broken links and lint errors from review feedback ([0a6343a](https://github.com/tansuasici/dev-playbook/commit/0a6343a17a5f4dd7b7a8c31fe79ee340ef14724b))

## [1.0.0] - 2025-03-16

### Added

- **Process guides**: branching, commits, issues, pull requests, code review, CI/CD, board workflow, incident response, dependency management
- **Convention guides**: .NET, Next.js, Python, naming, API design, API error format, testing, logging, Docker, git hooks, observability, environment management, documentation, error handling, performance, accessibility, i18n
- **Architecture guides**: ADR, database, security, multi-tenancy
- **Checklists**: new project setup, onboarding, pre-merge, release, security audit
- **Templates**: feature issue, bug issue, PR template, CLAUDE.md starter
- **Open-source infrastructure**: LICENSE (MIT), CONTRIBUTING.md, CODE_OF_CONDUCT.md
- **GitHub integration**: issue templates (YAML), PR template, CI workflow (markdown lint + link check)
- **Standard label set**: labels.yml with type, priority, status categories
