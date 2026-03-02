# Multi-Tenant Patterns

## School-Scoped Data Isolation

All tenant-specific data is isolated by `school_id`. This is enforced at the application level, not via RLS.

### Schema Level

Every tenant-scoped table includes:

```typescript
schoolId: uuid("school_id")
  .notNull()
  .references(() => schools.id, { onDelete: "cascade" }),
```

### Query Level

Every query for tenant data must filter by `schoolId`:

```typescript
// Correct — always filter by school
const students = await db.query.students.findMany({
  where: eq(students.schoolId, schoolId),
});

// WRONG — returns data from all schools
const students = await db.query.students.findMany();
```

### Service Level

Services receive `schoolId` as a parameter and pass it to all queries:

```typescript
async function getStudents(schoolId: string) {
  return db.query.students.findMany({
    where: eq(students.schoolId, schoolId),
  });
}
```

### Action Level

Server actions extract `schoolId` from the request context and pass it down:

```typescript
export async function listStudents(schoolId: string) {
  await requireSchoolRole(schoolId, ["admin", "teacher"]);
  return studentService.getStudents(schoolId);
}
```

## Cross-Tenant Data

Some tables are global (not school-scoped):

- User accounts (a user can belong to multiple schools)
- System configuration
- Shared reference data

These tables do NOT have a `schoolId` column. Access is controlled by authentication and role checks.
