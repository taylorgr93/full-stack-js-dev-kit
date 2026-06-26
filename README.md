# full-stack-js-dev-kit

Full-stack JavaScript/TypeScript dev kit for Claude Code. Code standards, code review, and git workflow skills for Node.js, React, Next.js, NestJS, and Express projects.

## Quick Start

```bash
# Add the marketplace (one time)
/plugin marketplace add taylorgr93/full-stack-js-dev-kit

# Install the skills
/plugin install code-standards@full-stack-js-dev-kit
/plugin install code-review@full-stack-js-dev-kit
/plugin install git-workflow@full-stack-js-dev-kit
```

## Skills Included

| Skill | Command | Description |
|-------|---------|-------------|
| **code-standards** | `/code-standards` | Naming conventions, TypeScript guidelines, ESLint + Prettier config, folder structure, error handling, KISS principles |
| **code-review** | `/code-review` | Full review checklist covering security, bugs, performance, error handling, architecture, and maintainability |
| **git-workflow** | `/git-workflow` | Conventional Commits, branch naming, PR templates, versioning, release workflow |

## Usage

Skills activate automatically when relevant. For example:

- Ask Claude to write code → `code-standards` applies automatically
- Ask Claude to review a PR or diff → `code-review` activates
- Ask Claude to create a commit message → `git-workflow` activates

You can also invoke them directly:

```bash
/code-review          # Review current changes
/git-workflow         # Help with git operations
/code-standards       # Check standards reference
```

## Stack Coverage

- **Backend**: Node.js, Express, NestJS
- **Frontend**: React, Next.js
- **Language**: TypeScript / JavaScript
- **Database**: MongoDB / Mongoose
- **Infrastructure**: Docker, CI/CD
- **Tooling**: ESLint, Prettier, Conventional Commits

## License

MIT
