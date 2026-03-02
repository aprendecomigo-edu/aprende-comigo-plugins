# Server Action Patterns

Third-person description: Guides the developer through creating Next.js server actions with proper authorization, validation, error handling, and service delegation patterns used in the Aprende Comigo codebase. Activates when the developer needs to "create a server action", "add a form action", "implement a mutation", or "add a new action".

---

## Server Action Structure

Every server action follows this consistent pattern:

1. **`"use server"` directive** at the top of the file
2. **Authentication guard** — call `requireAuth()` or `requireSchoolRole(schoolId, allowedRoles)` before any logic
3. **Input validation** — parse inputs with a Zod schema
4. **Service delegation** — call a service function for business logic
5. **Response formatting** — return `createSuccessResponse(data)` or `createErrorResponse(message)`

## Authorization Guards

Use the appropriate guard for every action:

- `requireAuth()` — verifies the user is logged in, returns the authenticated user
- `requireSchoolRole(schoolId, ['admin', 'teacher'])` — verifies the user has one of the specified roles at the given school

Never skip authorization. Never check roles manually — use the established helpers.

## Input Validation with Zod

Define Zod schemas in `lib/schemas/` and import them into the action:

- Validate all user inputs before processing
- Use `.parse()` or `.safeParse()` depending on error handling needs
- Keep schemas reusable — share between client-side form validation and server-side actions
- Use Zod v4 features (check project's Zod version)

## Error Handling

Use the established response helpers:

- `createSuccessResponse(data)` — wraps data in a success envelope
- `createErrorResponse(message)` — wraps error message in a failure envelope
- Catch service errors and return user-friendly messages
- Never expose internal error details to the client

## Service Delegation

Server actions should be thin — delegate business logic to service functions:

- Services live in `services/` or `lib/` directories
- Services handle database queries, transactions, and complex logic
- Services are reusable across multiple actions and API routes
- Use Drizzle ORM transactions for operations that modify multiple tables

## Transaction Patterns

For operations that modify multiple tables:

- Use `db.transaction(async (tx) => { ... })` to ensure atomicity
- Pass the transaction object (`tx`) to all queries within the transaction
- Roll back automatically on error

## References

- [auth-patterns.md](references/auth-patterns.md) — detailed authorization guard patterns and role hierarchy
- [error-handling.md](references/error-handling.md) — error response patterns and user-facing messages

## Examples

- [crud-action.md](examples/crud-action.md) — complete CRUD server action with auth, validation, and error handling
