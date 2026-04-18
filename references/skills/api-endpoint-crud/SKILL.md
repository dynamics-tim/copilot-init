---
name: api-endpoint-crud
description: Scaffolds a full CRUD endpoint for an Express/Node.js API — creates the route handler, Zod validation schema, Knex migration, and integration test, then registers the route in the central index
allowed-tools:
  - read
  - edit
  - shell
---

<!-- evaluated-by: skill-extractor -->

# API Endpoint CRUD

## When to Use

When scaffolding a new RESTful resource endpoint in an Express API that uses Knex
for database access and Zod for request validation. Covers the full lifecycle: migration,
validation schema, route handler with all CRUD operations, integration test, and route
registration.

Do NOT use for non-CRUD endpoints (webhooks, auth flows, file uploads) or for adding
individual operations to an existing resource — this skill creates the complete set.

## Workflow

1. **Read the existing route index** — Use `read` on `src/routes/index.ts` to understand the current registration pattern and import style.
2. **Read a sibling route for conventions** — Use `read` on the most recently modified `src/routes/*.ts` handler to match naming, error handling, and response format conventions.
3. **Create the Zod schema** — Use `edit` to create `src/schemas/<resource>.schema.ts` with `create`, `update`, and `params` schemas. Export a `validated` middleware wrapper for each.
4. **Create the Knex migration** — Use `shell` to run `npx knex migrate:make create_<resource>_table` then `edit` the generated file to define columns matching the Zod schema.
5. **Run the migration** — Use `shell` to run `npx knex migrate:latest --env test` to apply the migration to the test database.
6. **Create the route handler** — Use `edit` to create `src/routes/<resource>.ts` with GET (list + single), POST, PUT, and DELETE handlers. Wire Zod validation middleware on POST and PUT.
7. **Register the route** — Use `edit` on `src/routes/index.ts` to add the import and `router.use('/<resource>', <resource>Router)` line in alphabetical order.
8. **Create the integration test** — Use `edit` to create `src/routes/__tests__/<resource>.test.ts` with tests for each CRUD operation plus validation error cases.
9. **Run targeted tests** — Use `shell` to run `npm test -- --grep="<Resource>"` and verify all tests pass.

## File Patterns

- `src/routes/*.ts` — Express route handlers
- `src/routes/__tests__/*.test.ts` — Integration tests for routes
- `src/routes/index.ts` — Central route registration file
- `src/schemas/*.schema.ts` — Zod validation schemas
- `migrations/*_create_*_table.ts` — Knex migration files

## Rules

- Always create all four files (schema, migration, handler, test) in a single workflow — partial scaffolds cause confusion
- Match the import style and error response format from the sibling route exactly
- Register routes in alphabetical order in `src/routes/index.ts` to keep diffs clean
- Name the migration with the `create_<resource>_table` convention so rollbacks are discoverable
- Include at least one validation error test (missing required field) and one 404 test (resource not found)
- Run migrations against the test database before running tests — never skip this step

## Revision History

| Version | Date | Change | Reason |
|---------|------|--------|--------|
| v1 | 2025-01-15 | Initial extraction from 3 repeated sessions | Detected scaffold pattern: schema → handler → test |
| v2 | 2025-02-02 | Added step 7 "Register the route" | Usage drift: users manually edited `index.ts` after every scaffold (4/4 sessions) |
| v2 | 2025-02-02 | Added step 1 "Read the existing route index" | Needed context for step 7; without it the registration used wrong import style |
| v2 | 2025-02-02 | Narrowed trigger: added "Do NOT use for" exclusions | False-positive activations on webhook and auth endpoint tasks (2 misfires in 12 sessions) |
| v3 | 2025-02-20 | Added Knex migration steps (4–5) | Workflow drift: 3/5 recent sessions included migration creation, indicating the pattern expanded |
| v3 | 2025-02-20 | Added "alphabetical order" rule for route registration | Code review feedback: unordered registrations caused merge conflicts across branches |
