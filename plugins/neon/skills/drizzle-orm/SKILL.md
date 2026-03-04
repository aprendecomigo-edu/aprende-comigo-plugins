---
name: drizzle-orm
description: >
  Type-safe SQL toolkit for TypeScript. Covers schema declaration with pgTable, relational queries, migrations with drizzle-kit, Neon database drivers (HTTP and WebSocket), Zod integration, and query patterns. Use for any Drizzle ORM questions including schema design, query building, migration workflows, and configuration.
---

# Drizzle ORM

Drizzle is a headless TypeScript ORM with a SQL-first philosophy. Zero dependencies, 31KB, serverless-ready. It provides two query interfaces: SQL-like (`db.select().from()`) and relational (`db.query.table.findMany()`).

## Drizzle Documentation

Fetch any Drizzle doc page as markdown by appending the path to the base URL. Use the docs index to find pages — don't guess URLs.

Docs index: https://orm.drizzle.team/docs/overview

## Schema Declaration

Define tables using `pgTable` from `drizzle-orm/pg-core`. Export all models so drizzle-kit can process them for migrations.

### Table Definition

```typescript
import { pgTable, integer, varchar, timestamp, boolean } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: integer().primaryKey().generatedAlwaysAsIdentity(),
  name: varchar({ length: 255 }).notNull(),
  email: varchar({ length: 255 }).notNull().unique(),
  createdAt: timestamp().defaultNow().notNull(),
  updatedAt: timestamp().defaultNow().notNull(),
});
```

### Column Naming

Use `casing: 'snake_case'` in the drizzle config to auto-map TypeScript camelCase to database snake_case:

```typescript
const db = drizzle({ connection: process.env.DATABASE_URL, casing: "snake_case" });
```

### Reusable Column Patterns

Extract common fields into shared objects:

```typescript
const timestamps = {
  createdAt: timestamp().defaultNow().notNull(),
  updatedAt: timestamp().defaultNow().notNull(),
};

export const students = pgTable("students", {
  id: integer().primaryKey().generatedAlwaysAsIdentity(),
  schoolId: integer("school_id").notNull(),
  ...timestamps,
});
```

### Indexes and Constraints

Define in the third argument to `pgTable`:

```typescript
export const posts = pgTable("posts", {
  id: integer().primaryKey().generatedAlwaysAsIdentity(),
  slug: varchar({ length: 256 }).notNull(),
  authorId: integer("author_id").references(() => users.id),
}, (table) => [
  uniqueIndex("posts_slug_idx").on(table.slug),
  index("posts_author_idx").on(table.authorId),
]);
```

### Schema Organization

Multi-file approach for larger projects:

```
lib/db/schema/
├── users.ts
├── students.ts
├── schools.ts
└── index.ts        # re-exports all tables and relations
```

Configure in `drizzle.config.ts`:

```typescript
export default defineConfig({
  dialect: "postgresql",
  schema: "./lib/db/schema",
});
```

## Relations

Define relations separately from tables using the `relations()` function:

```typescript
import { relations } from "drizzle-orm";

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts),
}));

export const postsRelations = relations(posts, ({ one, many }) => ({
  author: one(users, { fields: [posts.authorId], references: [users.id] }),
  comments: many(comments),
}));
```

Relations enable the relational query API (`db.query`) but don't create foreign keys in the database — use `.references()` on columns for that.

## Query Patterns

### SQL-Like API

```typescript
import { eq, and, gt, like, inArray } from "drizzle-orm";

// Select
const result = await db.select().from(users).where(eq(users.id, 1));

// Join
const data = await db.select()
  .from(posts)
  .innerJoin(users, eq(posts.authorId, users.id))
  .where(eq(users.schoolId, schoolId));

// Insert
await db.insert(users).values({ name: "Ana", email: "ana@example.com" });

// Update
await db.update(users).set({ name: "Updated" }).where(eq(users.id, 1));

// Delete
await db.delete(users).where(eq(users.id, 1));
```

### Relational Query API

Use `db.query` for nested data fetching (single SQL query under the hood):

```typescript
// Fetch with relations
const usersWithPosts = await db.query.users.findMany({
  with: { posts: { with: { comments: true } } },
  where: (users, { eq }) => eq(users.schoolId, schoolId),
  orderBy: (users, { asc }) => [asc(users.name)],
  limit: 10,
});

// Partial select
const names = await db.query.users.findMany({
  columns: { id: true, name: true },
});

// Find first
const user = await db.query.users.findFirst({
  where: (users, { eq }) => eq(users.email, email),
});
```

### Transactions

```typescript
await db.transaction(async (tx) => {
  const [user] = await tx.insert(users).values({ name: "Ana" }).returning();
  await tx.insert(profiles).values({ userId: user.id, bio: "Hello" });
});
```

### Prepared Statements

```typescript
import { placeholder } from "drizzle-orm";

const prepared = db.query.users.findMany({
  where: (users, { eq }) => eq(users.schoolId, placeholder("schoolId")),
}).prepare("users_by_school");

const result = await prepared.execute({ schoolId: 42 });
```

## Neon Connection Drivers

### HTTP Driver (serverless, single queries)

Best for Next.js server components, API routes, edge functions:

```typescript
import { drizzle } from "drizzle-orm/neon-http";
import * as schema from "./schema";

export const db = drizzle(process.env.DATABASE_URL!, { schema, casing: "snake_case" });
```

Use the **pooled** connection string (`-pooler` endpoint) for serverless.

### WebSocket Driver (interactive transactions)

For scenarios requiring multi-statement transactions:

```typescript
import { drizzle } from "drizzle-orm/neon-serverless";
import * as schema from "./schema";

export const db = drizzle(process.env.DATABASE_URL!, { schema, casing: "snake_case" });
```

### Node.js Driver (long-running servers)

For traditional Node.js servers:

```typescript
import { drizzle } from "drizzle-orm/node-postgres";
```

## Drizzle Kit (Migrations)

### Configuration

```typescript
// drizzle.config.ts
import { defineConfig } from "drizzle-kit";

export default defineConfig({
  dialect: "postgresql",
  schema: "./lib/db/schema",
  out: "./drizzle",
  dbCredentials: {
    url: process.env.DATABASE_URL!,  // Use direct (non-pooled) URL for migrations
  },
  casing: "snake_case",
});
```

### Key Commands

- **`npx drizzle-kit generate`** — Generate SQL migration files from schema changes
- **`npx drizzle-kit migrate`** — Apply pending migrations sequentially
- **`npx drizzle-kit push`** — Push schema directly (dev only, no migration files)
- **`npx drizzle-kit pull`** — Introspect existing database into Drizzle schema
- **`npx drizzle-kit studio`** — Launch web UI for browsing database
- **`npx drizzle-kit check`** — Validate migrations for conflicts

### Migration Workflow

For production: `generate` then `migrate` (creates reviewable SQL files).
For development: `push` directly (faster iteration, no migration files).

Use the **direct (non-pooled)** connection string for drizzle-kit commands since they need DDL capabilities that PgBouncer's transaction mode doesn't support.

## Zod Integration

Generate Zod schemas from Drizzle tables for form validation:

```typescript
import { createInsertSchema, createSelectSchema } from "drizzle-zod";

export const insertUserSchema = createInsertSchema(users);
export const selectUserSchema = createSelectSchema(users);

// With refinements
export const createStudentSchema = createInsertSchema(students, {
  name: z.string().min(1).max(255),
  email: z.string().email(),
}).omit({ id: true, createdAt: true, updatedAt: true });
```

Requires: `drizzle-zod` package.
