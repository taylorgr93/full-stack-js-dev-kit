---
name: code-review
description: >
  Code review checklist for JavaScript/TypeScript full-stack projects. Use this
  skill when reviewing code, PRs, diffs, or when the user asks to review, audit,
  check code quality, find bugs, check correctness, or review architecture.
  Covers security, bugs, performance, readability, error handling, architecture,
  and maintainability for Node.js, React, Next.js, NestJS, and Express projects.
---

# Code Review

Always read the target file(s) with the Read tool first, then apply the criteria
below against the actual code. Do not opine without reading.

Focus on what matters: bugs, security, and maintainability. Don't nitpick
style — that's the linter's job.

## Review Criteria (by priority)

### 1. Security

- No hardcoded secrets, API keys, or credentials
- Input validation and sanitization before use
- SQL/NoSQL injection prevention (parameterized queries, no string concatenation in queries)
- No `eval()`, `Function()`, or dynamic code execution with user input
- Auth checks present on protected routes/endpoints
- Sensitive data not logged or exposed in error responses
- CORS configured properly (not `*` in production)
- Rate limiting on public endpoints
- File uploads validated (type, size, name sanitization)

### 2. Bugs & Correctness

- Edge cases handled (null, undefined, empty arrays, empty strings)
- Async operations have proper error handling (try/catch or .catch())
- No unhandled promise rejections
- Race conditions considered in concurrent operations
- Correct HTTP status codes used (don't return 200 for errors)
- Database operations use transactions where needed
- Pagination implemented correctly (no offset on large datasets)

### 3. Error Handling

- Try/catch in all controllers and async operations
- Custom error classes with correct HTTP status codes
- Full error logged server-side, safe message to client
- Error messages include context (what was happening, what input caused it)
- Don't catch errors just to re-throw without adding context

### 4. Performance

- No N+1 query patterns (use populate/aggregation/joins)
- Database queries have proper indexes
- No unnecessary `await` in loops (use `Promise.all` for parallel ops)
- Large datasets are streamed, not loaded entirely in memory
- Expensive computations are cached where appropriate
- No memory leaks (event listeners cleaned up, intervals cleared)
- React: no unnecessary re-renders (proper deps in useEffect, memoization where needed)

### 5. Architecture

- Changes are in the right layer (controller vs service vs repository)
- No business logic in controllers/routes
- No direct database calls from controllers
- Shared logic extracted to services/utils, not duplicated
- New dependencies justified (no adding a lib for something trivial)
- API contracts are backward compatible (or breaking changes are documented)

### 6. Code Quality

- Functions do one thing and are reasonably sized (~40 lines max)
- Variable and function names are descriptive and follow conventions
- No commented-out code (use git history)
- No magic numbers — use named constants
- Comments in English, explaining WHY not WHAT
- No hardcoded values — use environment variables
- No code duplication

## Multi-Agent Review Workflow

When reviewing **more than 3 files**, launch 3 subagents in parallel (model:
opus) to speed up the review. Each agent handles a subset of criteria:

| Agent | Criteria |
|-------|----------|
| **Agent 1 — Security & Bugs** | Sections 1 (Security) and 2 (Bugs & Correctness) |
| **Agent 2 — Reliability** | Sections 3 (Error Handling) and 4 (Performance) |
| **Agent 3 — Design** | Sections 5 (Architecture) and 6 (Code Quality) |

Each agent must:
1. Read the target files with the Read tool
2. Apply **only** its assigned criteria from this skill
3. Return findings in the standard format (Severity / File / Issue / Fix)

After all 3 agents complete, consolidate their findings, deduplicate any
overlapping issues, and append the "What's Good" section.

For **3 files or fewer**, run the review inline without subagents — the
overhead is not worth it for small reviews.

## Automated Checks

If the project has them configured, run these and include relevant findings:

- **ESLint**: `npm run lint` (if the script exists in package.json) — catches style and common bug patterns
- **npm audit**: `npm audit --audit-level=high` — flag only HIGH and CRITICAL vulnerabilities, ignore low/moderate transitive noise

Do not block the review if these tools are not configured or return noisy results.

## Output Format

For each finding:

- **Severity**: 🔴 CRITICAL / 🟠 HIGH / 🟡 MEDIUM / 🟢 LOW
- **File**: path
- **Issue**: description
- **Fix**: suggested solution

### What's Good

At the end of all findings, include a "What's Good" section highlighting
positive patterns and good practices found in the code. Always find something
to praise — reinforce what's done well, not just what's wrong.

## Review Principles

- **Be constructive** — suggest solutions, don't just point out problems
- **Pick your battles** — focus on things that matter, not personal preferences
- **Assume good intent** — the author likely had reasons for their choices
- **Don't rewrite** — review the code as-is, suggest improvements within the existing structure
- **Praise good patterns** — reinforce what's done well, not just what's wrong
