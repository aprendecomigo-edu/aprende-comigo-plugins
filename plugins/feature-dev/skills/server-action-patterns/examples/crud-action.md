# CRUD Server Action Example

## Create Action

```typescript
"use server";

import { requireSchoolRole } from "@/lib/auth";
import { createStudentSchema } from "@/lib/schemas/student";
import { studentService } from "@/services/student-service";
import { createSuccessResponse, createErrorResponse } from "@/lib/responses";

export async function createStudent(schoolId: string, formData: FormData) {
  // 1. Authorization
  await requireSchoolRole(schoolId, ["admin", "teacher"]);

  // 2. Validation
  const raw = Object.fromEntries(formData);
  const result = createStudentSchema.safeParse(raw);
  if (!result.success) {
    return createErrorResponse(result.error.issues[0].message);
  }

  // 3. Service delegation
  try {
    const student = await studentService.create(schoolId, result.data);
    return createSuccessResponse({ student });
  } catch (error) {
    return createErrorResponse("Failed to create student. Please try again.");
  }
}
```

## Update Action

```typescript
"use server";

import { requireSchoolRole } from "@/lib/auth";
import { updateStudentSchema } from "@/lib/schemas/student";
import { studentService } from "@/services/student-service";
import { createSuccessResponse, createErrorResponse } from "@/lib/responses";

export async function updateStudent(
  schoolId: string,
  studentId: string,
  formData: FormData
) {
  await requireSchoolRole(schoolId, ["admin", "teacher"]);

  const raw = Object.fromEntries(formData);
  const result = updateStudentSchema.safeParse(raw);
  if (!result.success) {
    return createErrorResponse(result.error.issues[0].message);
  }

  try {
    const student = await studentService.update(schoolId, studentId, result.data);
    return createSuccessResponse({ student });
  } catch (error) {
    return createErrorResponse("Failed to update student. Please try again.");
  }
}
```

## Delete Action

```typescript
"use server";

import { requireSchoolRole } from "@/lib/auth";
import { studentService } from "@/services/student-service";
import { createSuccessResponse, createErrorResponse } from "@/lib/responses";

export async function deleteStudent(schoolId: string, studentId: string) {
  // Only admins can delete
  await requireSchoolRole(schoolId, ["admin"]);

  try {
    await studentService.delete(schoolId, studentId);
    return createSuccessResponse({ deleted: true });
  } catch (error) {
    return createErrorResponse("Failed to delete student. Please try again.");
  }
}
```
