# Database Schema Patterns

Third-person description: Guides the developer through creating Drizzle ORM schemas, multi-tenant table design, migration files, relations, and indexes for a Next.js + Supabase project. Activates when the developer needs to "add a database table", "create a schema", "add a migration", "define a new model", or "modify the database".

---

## Drizzle Schema Design

Schemas are defined in `db/schema/` using Drizzle ORM's TypeScript DSL:

1. **Define the table** with columns, types, defaults, and constraints
2. **Add relations** to connect tables via foreign keys
3. **Add indexes** for frequently queried columns
4. **Export** the table and relations from the schema barrel file
5. **Create migration files** to apply the schema to the database

## Multi-Tenant Isolation

Every table that stores tenant-specific data must include a `school_id` column:

- Add `schoolId` as a required column with a foreign key to the schools table
- Add an index on `schoolId` for query performance
- Add a compound index on `(schoolId, <primary-lookup-column>)` for common queries
- Never rely on RLS — enforce school-scoped filtering in application code (services and server actions)

## Table Structure Conventions

- Use `pgTable` from Drizzle
- Primary keys: UUID with `defaultRandom()`
- Timestamps: `createdAt` and `updatedAt` columns with defaults
- Foreign keys: explicitly defined with `references()` and appropriate `onDelete` behavior
- Enums: define with `pgEnum` for status fields and type discriminators
- Naming: snake_case for database columns, camelCase for TypeScript properties

## Student Account Types

When working with student data, account for different account types:

- **Minor students** — managed by parents/guardians, may have restricted data access
- **Adult students** — self-managed accounts with full access
- The account type affects permissions, data visibility, and communication channels

## Relations

Define relations separately from tables using `relations()`:

- One-to-many: parent table has `many`, child table has `one`
- Many-to-many: use a junction table with two `one` relations
- Relations enable type-safe eager loading with `db.query.*.findMany({ with: { ... } })`

## Indexes

Add indexes for:

- Foreign key columns (especially `schoolId`)
- Columns used in `WHERE` clauses frequently
- Unique constraints (email within school, etc.)
- Compound indexes for multi-column lookups

## Two-File Migration Structure

Migrations consist of two files:

1. **SQL migration file** — the actual DDL statements (`CREATE TABLE`, `ALTER TABLE`, etc.)
2. **Snapshot file** — Drizzle's schema snapshot for diffing

Generate migrations with the Drizzle Kit CLI. Never edit snapshot files manually.

## References

- [migration-structure.md](references/migration-structure.md) — detailed migration file structure and generation workflow
- [multi-tenant-patterns.md](references/multi-tenant-patterns.md) — school-scoped data isolation patterns

## Examples

- [table-with-relations.md](examples/table-with-relations.md) — complete table definition with relations, indexes, and Zod schema
