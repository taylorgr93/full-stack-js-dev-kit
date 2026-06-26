---
name: code-standards
description: >
  JavaScript/TypeScript code standards and conventions for Node.js projects using
  React, Next.js, NestJS, Express, and MongoDB. Enforces naming conventions, file
  structure guidelines, ESLint + Prettier configuration, TypeScript best practices,
  and Keep It Simple principles. Use this skill whenever writing, reviewing, refactoring,
  or generating code in any JS/TS project. Also trigger when the user asks about
  coding style, naming conventions, folder structure, linting setup, formatting rules,
  or best practices for their stack. This skill applies even if the user doesn't
  explicitly mention "standards" — any code generation or review task should follow
  these conventions.
---

# Code Standards

These are the coding standards for all JavaScript/TypeScript projects. Follow them
when writing, reviewing, or refactoring code. The guiding philosophy is **Keep It Simple** —
prefer readable, pragmatic code over clever abstractions.

## Core Principles

1. **KISS over cleverness** — If a junior dev can't understand it in 30 seconds, simplify it.
2. **No over-engineering** — Don't add abstractions, patterns, or layers until they're actually needed. YAGNI.
3. **Comments in English** — All code comments, JSDoc, and documentation must be in English.
4. **Respect existing code** — When modifying existing functions, preserve variable names, log messages, and overall structure unless explicitly asked to create something new.
5. **Pragmatic TypeScript** — Use types to prevent bugs, not to impress. `any` is acceptable when the cost of typing exceeds the benefit, but prefer `unknown` when possible.

## Naming Conventions

### Variables and Functions
- **camelCase** for variables, functions, and method names: `getUserById`, `isActive`, `totalAmount`
- **PascalCase** for classes, interfaces, types, enums, and React components: `UserService`, `IUserResponse`, `PaymentStatus`
- **UPPER_SNAKE_CASE** for constants and env variables: `MAX_RETRIES`, `API_BASE_URL`
- **kebab-case** for file and folder names: `user-service.ts`, `payment-gateway/`

### Prefixes and Suffixes
- Interfaces: prefix with `I` only when it helps distinguish from classes (e.g., `IUserRepository` vs `UserRepository`). For standalone types/interfaces that don't shadow a class, skip the prefix.
- Booleans: prefix with `is`, `has`, `can`, `should`: `isActive`, `hasPermission`, `canEdit`
- Event handlers: prefix with `handle` in React: `handleSubmit`, `handleClick`
- Async functions: name should imply the operation, no need for `Async` suffix

### What to Avoid
- Single letter variables (except `i`, `j` in loops, `e` for errors/events, `_` for unused)
- Hungarian notation (`strName`, `arrItems`)
- Abbreviations that aren't universally known (`usr` → `user`, `btn` is OK)
- Generic names: `data`, `info`, `item`, `temp`, `result` — be specific about what it holds

## TypeScript Guidelines

### Type Strictness: Pragmatic
- Enable `strict: true` in tsconfig but allow flexibility per project
- `any` is allowed when properly justified (third-party libs without types, rapid prototyping, complex dynamic shapes). Add a comment explaining why: `// any: MercadoPago SDK lacks proper types`
- Prefer `unknown` over `any` when you'll narrow the type anyway
- Don't over-type: if TypeScript can infer it, let it. No need for `const x: string = 'hello'`
- Use `Record<string, T>` over `{ [key: string]: T }` for cleaner syntax

### Interfaces and Types
- Use `interface` for object shapes that might be extended
- Use `type` for unions, intersections, and computed types
- Export types/interfaces from a shared `types/` or co-locate with the module that owns them
- Avoid deep nesting of generics — if it's unreadable, break it into named types

### Enums
- Prefer `const enum` or string literal unions over regular enums when possible
- If using enums, use PascalCase for the enum name and values

```typescript
// Preferred: union type (simpler, tree-shakeable)
type PaymentStatus = 'pending' | 'completed' | 'failed';

// Also OK: enum when you need runtime access
enum PaymentStatus {
  Pending = 'pending',
  Completed = 'completed',
  Failed = 'failed',
}
```

## Functions and Methods

- **Small and focused** — One function, one job. If it needs a comment explaining "this part does X and then Y", split it.
- **Max ~40 lines** per function as a soft guideline. If it's longer, look for extraction opportunities.
- **Early returns** over nested if/else. Reduce indentation levels.
- **Destructure** parameters when a function takes 3+ related args — use an options object instead.
- **Default parameters** over manual `|| fallback` patterns.

```typescript
// Preferred: early return + destructuring
async function createUser({ email, name, role = 'user' }: CreateUserParams) {
  if (!email) throw new BadRequestError('Email is required');

  const existingUser = await userRepo.findByEmail(email);
  if (existingUser) throw new ConflictError('User already exists');

  return userRepo.create({ email, name, role });
}

// Avoid: deeply nested
async function createUser(email: string, name: string, role: string) {
  if (email) {
    const existingUser = await userRepo.findByEmail(email);
    if (!existingUser) {
      return userRepo.create({ email, name, role: role || 'user' });
    } else {
      throw new ConflictError('User already exists');
    }
  } else {
    throw new BadRequestError('Email is required');
  }
}
```

## Error Handling

- **Always** catch errors in async operations — never let promises float unhandled
- Use custom error classes that extend a `BaseError` with `statusCode` and `message`
- Log errors with context: what was happening, what input caused it
- In Express/NestJS: let errors bubble up to a centralized error handler middleware
- Don't catch errors just to re-throw them without adding context

```typescript
// Good: meaningful error with context
try {
  const payment = await processPayment(orderId, amount);
  return payment;
} catch (error) {
  logger.error('Payment processing failed', { orderId, amount, error });
  throw new PaymentError(`Failed to process payment for order ${orderId}`);
}

// Avoid: swallowing the error
try {
  await processPayment(orderId, amount);
} catch (error) {
  console.log('error');  // Useless
}
```

## Imports

- **Order**: Node built-ins → external packages → internal absolute paths → relative imports
- **Separate** each group with a blank line
- **Prefer** named exports over default exports (except React page/layout components in Next.js)
- **No barrel files** (`index.ts` re-exporting everything) unless the module is a public API — they hurt tree-shaking and create circular dependencies

```typescript
import { readFile } from 'node:fs/promises';

import express from 'express';
import { Injectable } from '@nestjs/common';

import { UserService } from '@/services/user-service';
import { logger } from '@/utils/logger';

import { validateEmail } from './helpers';
```

## Folder Structure

Don't force a single structure — adapt to the project's needs and framework. However, these patterns work well:

**For NestJS / backend APIs**: feature-based modules (NestJS default)
```
src/
  auth/
    auth.controller.ts
    auth.service.ts
    auth.module.ts
    dto/
    guards/
  users/
    users.controller.ts
    users.service.ts
  common/
    filters/
    interceptors/
    decorators/
```

**For Next.js / React**: co-locate components with their pages
```
app/
  (auth)/
    login/
    register/
  dashboard/
components/
  ui/           ← reusable primitives
  features/     ← domain-specific components
lib/
  utils.ts
  constants.ts
types/
```

**For Express APIs**: flexible, keep related things together
```
src/
  routes/
  controllers/
  services/
  models/
  middlewares/
  utils/
```

## Logging

- Use a proper logger (`winston`, `pino`, or NestJS built-in `Logger`) — not raw `console.log` in production code
- `console.log` is fine for local debugging but remove before committing
- Log levels: `error` for failures, `warn` for recoverable issues, `info` for business events, `debug` for dev details
- Include structured context in logs (objects, not string concatenation)

```typescript
// Good
logger.info('User created successfully', { userId: user.id, email: user.email });
logger.error('Database connection failed', { host, port, error: err.message });

// Avoid
console.log('user created: ' + user.id);
console.log('error', error);
```

## Async Patterns

- **Always** use `async/await` over `.then()` chains
- Use `Promise.all()` for independent parallel operations
- Use `Promise.allSettled()` when you need all results regardless of individual failures
- **Never** mix callbacks and promises in the same flow

## ESLint + Prettier

See `references/eslint-prettier-config.md` for the recommended base configuration.

Key rules enforced:
- No unused variables (warn, not error — allow `_` prefix for intentionally unused)
- No explicit `any` as warning (not error, since we allow justified usage)
- Consistent return types on exported functions
- Prettier handles all formatting — no formatting debates

## Git & Code Hygiene

- No commented-out code in commits — use git history instead
- No `TODO` without a ticket/issue reference: `// TODO(#123): implement retry logic`
- No magic numbers — extract to named constants
- No hardcoded secrets or env values — use environment variables

## MongoDB Patterns

- Use Mongoose with TypeScript schemas or raw MongoDB driver with typed collections
- Always define indexes in schema/migration files, not ad-hoc
- Use `lean()` in Mongoose when you don't need document methods (performance)
- Prefer aggregation pipelines over multiple queries when the logic allows it
- Paginate with cursor-based (`_id` gt/lt) over skip/limit for large collections

## Docker

- Use multi-stage builds for production images
- Pin base image versions: `node:20-alpine` not `node:latest`
- Copy `package*.json` first, install deps, then copy source (layer caching)
- Use `.dockerignore` to exclude `node_modules`, `.git`, `.env`
- Don't run as root — use `USER node`
