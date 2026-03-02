# Authorization Patterns

## Guard Functions

### requireAuth()

Verifies the user is authenticated. Returns the current user object. Throws if not authenticated.

```typescript
const user = await requireAuth();
```

Use for actions that any authenticated user can perform (e.g., updating their own profile).

### requireSchoolRole(schoolId, roles)

Verifies the user has one of the specified roles at the given school. Returns the user and their role. Throws if unauthorized.

```typescript
const { user, role } = await requireSchoolRole(schoolId, ['admin', 'teacher']);
```

Use for school-scoped actions. Always pass the `schoolId` from the request — never trust a role without school context.

## Role Hierarchy

Roles are school-scoped — a user can be admin at one school and teacher at another:

- **admin** — full access to school management, financial operations, user management
- **teacher** — access to their own classes, students, schedules, and compensation
- **staff** — limited access to operational features

## Common Patterns

### School-scoped data filtering

After authorization, always filter queries by school_id:

```typescript
const students = await db.query.students.findMany({
  where: eq(students.schoolId, schoolId),
});
```

### Action-level role checks

Some actions need finer-grained checks beyond the guard:

```typescript
const { user, role } = await requireSchoolRole(schoolId, ['admin', 'teacher']);

// Only admins can delete
if (action === 'delete' && role !== 'admin') {
  return createErrorResponse('Only administrators can delete records');
}
```
