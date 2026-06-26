---
name: git-workflow
description: >
  Git workflow conventions for JS/TS projects. Use this skill when creating commits,
  branches, PRs, changelogs, or releases. Also triggers when the user asks about
  commit messages, branch naming, versioning, publish workflow, or any git-related
  conventions. Enforces Conventional Commits, semantic versioning, and clean git history.
---

# Git Workflow

Standards for git operations across all projects. The goal is a clean, readable
git history that tells the story of the project.

## Conventional Commits

All commit messages follow the Conventional Commits spec:

```
<type>(<scope>): <short description>

[optional body]

[optional footer]
```

### Types

| Type | When to use | Bumps |
|------|------------|-------|
| `feat` | New feature or capability | minor |
| `fix` | Bug fix | patch |
| `docs` | Documentation only | — |
| `style` | Formatting, semicolons, no code change | — |
| `refactor` | Code change that neither fixes a bug nor adds a feature | — |
| `perf` | Performance improvement | patch |
| `test` | Adding or fixing tests | — |
| `build` | Build system or external deps (npm, docker) | — |
| `ci` | CI/CD config (GitHub Actions, GitLab CI) | — |
| `chore` | Maintenance tasks, no production code change | — |
| `revert` | Reverts a previous commit | — |

### Scope

Optional but recommended. Use the module/feature name:

```bash
feat(auth): add Google OAuth provider
fix(payments): handle MercadoPago webhook timeout
docs(readme): update installation steps
refactor(users): extract validation to shared util
```

### Rules

- Subject line: imperative mood, lowercase, no period, max 72 chars
- Body: explain WHY, not WHAT (the diff shows what changed)
- Breaking changes: add `!` after type or `BREAKING CHANGE:` in footer

```bash
# Breaking change
feat(api)!: change user endpoint response format

BREAKING CHANGE: /api/users now returns paginated response instead of array.
Migration: wrap existing consumers with `.data` accessor.
```

## Branch Naming

```
<type>/<ticket-or-short-description>
```

### Examples

```bash
feat/google-oauth
fix/payment-webhook-timeout
refactor/user-validation
docs/api-endpoints
hotfix/critical-auth-bypass
release/v1.2.0
```

### Rules

- Lowercase, kebab-case
- Prefix matches commit types
- Include ticket number when available: `feat/JIRA-123-google-oauth`
- Keep it short but descriptive
- `main` (or `master`) is the production branch
- `develop` or `dev` for integration (if using gitflow)

## Pull Request Template

When creating PRs, use this structure:

```markdown
## What does this PR do?
[Brief description of the changes]

## Type of change
- [ ] feat: New feature
- [ ] fix: Bug fix
- [ ] refactor: Code refactoring
- [ ] docs: Documentation
- [ ] test: Tests
- [ ] chore: Maintenance

## How to test
1. [Step-by-step testing instructions]
2. [Include relevant endpoints, pages, or commands]

## Checklist
- [ ] Code follows project standards
- [ ] Self-reviewed my own code
- [ ] Added/updated tests (if applicable)
- [ ] No new warnings or errors
- [ ] Env variables documented (if new ones added)

## Screenshots (if UI changes)
[Before/After screenshots]
```

## Versioning (SemVer)

Follow Semantic Versioning: `MAJOR.MINOR.PATCH`

- **MAJOR** (1.0.0 → 2.0.0): Breaking changes, incompatible API changes
- **MINOR** (1.0.0 → 1.1.0): New features, backward compatible
- **PATCH** (1.0.0 → 1.0.1): Bug fixes, backward compatible

### Pre-release tags

```bash
1.0.0-alpha.1    # Early testing
1.0.0-beta.1     # Feature complete, testing
1.0.0-rc.1       # Release candidate
```

## Release Workflow

### For npm packages

```bash
# 1. Ensure clean working tree
git status

# 2. Run tests
npm test

# 3. Bump version (updates package.json + creates git tag)
npm version patch   # or minor / major

# 4. Push with tags
git push && git push --tags

# 5. Publish
npm publish
```

### For apps/services (Docker-based)

```bash
# 1. Merge PR to main
# 2. Tag the release
git tag -a v1.2.0 -m "Release v1.2.0: add payment webhooks"
git push --tags

# 3. CI/CD picks up the tag and deploys
```

## Changelog

Maintain a `CHANGELOG.md` at the root. Group by version, newest first:

```markdown
# Changelog

## [1.2.0] - 2025-12-15

### Added
- Google OAuth authentication (#45)
- Rate limiting on public endpoints (#48)

### Fixed
- MercadoPago webhook timeout on slow connections (#52)

### Changed
- User response format is now paginated (#50)

## [1.1.0] - 2025-11-20
...
```

## Git Hygiene

- **Squash commits** on feature branches before merging (keep history clean)
- **Rebase** feature branches on main before merging (avoid merge commits when possible)
- **Delete branches** after merge
- **Never force push** to `main` or shared branches
- **Tag releases** — every production deploy should have a version tag
- **Write meaningful commit messages** — your future self will thank you
