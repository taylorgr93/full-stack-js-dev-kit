# Project-Specific Code Review — Example Template

Copy this file to your project's `.claude/skills/code-review-project/SKILL.md`
and customize it with your stack-specific rules. This skill complements the
generic code-review skill with rules that only apply to your project.

```markdown
---
name: code-review-project
description: >
  Project-specific code review rules. Complements the generic code-review skill
  with stack-specific checks for this project's MongoDB, Express, and multi-tenancy
  patterns. Activates alongside code-review when reviewing code in this project.
---

# Project-Specific Review Rules

These rules extend the generic code-review skill. Apply them on top of the
base review criteria.

## MongoDB & MongoWrapper

- Use `MongoWrapper` (mongoclienteasywrapper) for all database operations
- Use `convertIdsToObjectId` for any field ending in `_id` before queries
- Prefer aggregation pipelines with `$facet` for paginated queries returning `{ Total, Results, Metadata }`
- Use `$in` instead of multiple `findOne` calls (avoid N+1)
- Always call `.lean()` when document methods are not needed

## Express & Error Handling

- All async route handlers must be wrapped with `catchErrAsync`
- Use custom `ApiError` class with correct HTTP status codes
- Error messages must come from `shared/locales/es.json` and `shared/locales/en.json` — never hardcoded
- Full error logged server-side, safe dictionary message to client

## Multi-Tenancy

- All database queries must be scoped by `companies_id`
- Verify `companies_id` is extracted from the authenticated user context, never from request body
- Check that cross-tenant data access is impossible through the query path

## Middlewares

- Reuse existing middlewares: `headersApigw`, `filterPermissions`
- Input validation: use Yup schemas in Express, class-validator in NestJS
- Auth: validate JWT and check `headersApigw` injected headers from API gateway

## API Patterns

- Response format: `{ success: true, data: ... }` for success, `{ success: false, message: ... }` for errors
- Pagination response: `{ Total, Results, Metadata }` via `$facet`
- Use proper HTTP methods and status codes (201 for created, 204 for no content, etc.)
```
