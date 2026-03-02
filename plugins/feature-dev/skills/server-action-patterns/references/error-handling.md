# Error Handling Patterns

## Response Helpers

### createSuccessResponse(data)

Wraps successful results in a consistent envelope:

```typescript
return createSuccessResponse({ student: createdStudent });
```

### createErrorResponse(message)

Wraps error messages in a consistent envelope:

```typescript
return createErrorResponse('Student with this email already exists');
```

## Error Handling Strategy

### Validation Errors

Use Zod's `.safeParse()` to catch validation errors gracefully:

```typescript
const result = schema.safeParse(input);
if (!result.success) {
  return createErrorResponse(result.error.issues[0].message);
}
```

### Service Errors

Catch errors from service functions and return user-friendly messages:

```typescript
try {
  const data = await studentService.create(validated);
  return createSuccessResponse(data);
} catch (error) {
  if (error instanceof ConflictError) {
    return createErrorResponse('A record with this identifier already exists');
  }
  return createErrorResponse('An unexpected error occurred. Please try again.');
}
```

### Never Expose Internals

- Do not return stack traces, SQL errors, or internal error objects to the client
- Log detailed errors server-side for debugging
- Return generic messages for unexpected errors
