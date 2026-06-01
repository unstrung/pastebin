# Contributing to Pastebin

Thank you for your interest in contributing! Whether you're fixing a bug, proposing a
feature, improving documentation, or reviewing a pull request — every contribution
matters and is appreciated.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Ways to Contribute](#ways-to-contribute)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Commit Message Guidelines](#commit-message-guidelines)
- [Pull Request Process](#pull-request-process)
- [Reporting Bugs](#reporting-bugs)
- [Suggesting Features](#suggesting-features)
- [Style Guide](#style-guide)
- [Getting Help](#getting-help)

---

## Code of Conduct

This project is committed to a welcoming, respectful, and inclusive environment. All contributors are expected to:

- Use kind, constructive, and professional language
- Respect differing viewpoints and experiences
- Accept feedback gracefully and give it generously
- Focus on what is best for the community and the project

Unacceptable behavior will result in removal from the project. Report issues to the maintainer privately.

---

## Ways to Contribute

| Contribution Type | How |
|-------------------|-----|
| 🐛 Bug fix | Open an issue first, then submit a PR |
| ✨ New feature | Open a Feature Request issue, discuss, then PR |
| 📝 Documentation | Fix typos, improve clarity, add examples |
| 🧪 Tests | Add missing coverage or improve existing tests |
| 🔍 Code review | Review open pull requests and leave constructive feedback |
| 💬 Discussion | Comment on issues to add context or share experience |
| 🌍 Translation | Help translate the UI or docs into another language |

---

## Getting Started

### Prerequisites

- [Git](https://git-scm.com/)
- [Node.js](https://nodejs.org/) v20.x LTS or later
- [npm](https://www.npmjs.com/) v9+ (comes with Node.js)
- [Docker](https://www.docker.com/) and Docker Compose

### Fork and Clone

1. **Fork** the repository by clicking the **Fork** button on the GitHub page
2. **Clone** your fork locally:

    git clone https://github.com/YOUR_USERNAME/pastebin.git
    cd pastebin

3. **Add the upstream remote:**

    git remote add upstream https://github.com/ORIGINAL_OWNER/pastebin.git

### Install and Run

    npm install
    docker compose up -d
    npm run dev

The app will be available at http://localhost:3000.

---

## Development Workflow

Always work on a **dedicated branch** — never commit directly to `main`.

    # 1. Sync your fork
    git fetch upstream
    git checkout main
    git merge upstream/main

    # 2. Create a branch
    git checkout -b feat/your-feature-name

    # 3. Make your changes, then lint and test
    npm run lint
    npm test

    # 4. Commit and push
    git add .
    git commit -m "feat: add paste forking endpoint"
    git push origin feat/your-feature-name

    # 5. Open a Pull Request on GitHub

### Branch Naming

| Prefix | Example |
|--------|---------|
| `feat/` | `feat/oauth-login` |
| `fix/` | `fix/issue-88-403-unlisted` |
| `docs/` | `docs/update-api-reference` |
| `chore/` | `chore/upgrade-node-20` |
| `test/` | `test/paste-expiry-coverage` |
| `security/` | `security/xss-title-sanitize` |

---

## Commit Message Guidelines

This project follows [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/).

    <type>(<optional scope>): <short summary>

| Type | When to use |
|------|-------------|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation changes only |
| `refactor` | Code restructuring — no feature or bug change |
| `test` | Adding or updating tests |
| `chore` | Build process, dependencies, tooling |
| `security` | Security-related fix |
| `perf` | Performance improvement |

**Examples:**

    feat(api): add paste forking endpoint
    fix(auth): resolve 403 on unlisted pastes for anonymous users
    security: patch stored XSS in paste title field

    # Breaking change
    feat(api)!: replace page-based pagination with cursor-based

    BREAKING CHANGE: the `page` query param is removed; use `cursor` instead.

**Rules:**
- Use imperative mood: *"add endpoint"*, not *"added endpoint"*
- Keep summary under 72 characters
- Reference issues in the footer: `Closes #88`, `Fixes #91`

---

## Pull Request Process

1. **Link the issue** your PR addresses (`Closes #88`)
2. **Fill out the PR template** completely
3. **Keep PRs focused** — one logical change per PR
4. **Ensure all CI checks pass** before requesting review
5. **Respond to review comments** within a reasonable time
6. PRs require **at least one approving review** from a maintainer
7. Maintainers will **squash-merge** to keep history clean

### PR Description Template

    ## Summary
    <!-- What does this PR do and why? -->

    ## Changes
    - Added ...
    - Fixed ...
    - Updated ...

    ## Related Issues
    Closes #<issue number>

    ## Testing
    <!-- How did you test this? What cases did you cover? -->

    ## Screenshots (if applicable)

---

## Reporting Bugs

Use the [Bug Report template](.github/ISSUE_TEMPLATE/bug_report.md).

Before opening a new issue:
- Search existing issues to avoid duplicates
- Check the CHANGELOG to see if it was already fixed
- Provide clear, minimal reproduction steps

For **security vulnerabilities**, do **not** open a public issue — use
[GitHub Security Advisories](../../security/advisories/new) instead.

---

## Suggesting Features

Use the [Feature Request template](.github/ISSUE_TEMPLATE/feature_request.md).

Check the [Roadmap](ROADMAP.md) first — your idea may already be planned.

---

## Style Guide

### JavaScript / TypeScript
- Follow the project ESLint and Prettier config
- Run `npm run lint` to check, `npm run lint:fix` to auto-fix
- Prefer `async/await` over raw Promise chains
- All exports must include JSDoc comments

### API Design
- Follow RESTful conventions: nouns for resources, HTTP verbs for actions
- All endpoints must return `{ data, meta, errors }`
- New fields must be documented before the PR merges
- Deprecate before removing

### Tests
- All new features require unit and/or integration tests
- Aim for >= 80% coverage on new code
- Use descriptive names: "should return 403 when anonymous user accesses private paste"

### Documentation
- Update CHANGELOG.md under [Unreleased] for every user-facing change
- Use plain, clear English

---

## Getting Help

- **Open a Discussion** on GitHub (Questions & Answers category)
- **Comment on the relevant issue** if your question is context-specific
- We aim to respond within **48 hours** on business days

---

*Thank you for contributing to Pastebin — your time and effort make this project better for everyone.* 🙌
