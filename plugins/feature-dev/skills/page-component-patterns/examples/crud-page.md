# CRUD Page Example

## Server Page (page.tsx)

```typescript
import { getTranslations } from "next-intl/server";
import { requireSchoolRole } from "@/lib/auth";
import { studentService } from "@/services/student-service";
import { StudentTable } from "./_components/student-table";
import { CreateStudentButton } from "./_components/create-student-button";

interface Props {
  params: { schoolId: string; locale: string };
}

export default async function StudentsPage({ params }: Props) {
  const { schoolId } = await params;
  await requireSchoolRole(schoolId, ["admin", "teacher"]);
  const t = await getTranslations("students");

  const students = await studentService.getBySchool(schoolId);

  return (
    <div className="flex flex-col gap-6">
      <div className="flex items-center justify-between">
        <h1 className="text-2xl font-bold">{t("title")}</h1>
        <CreateStudentButton schoolId={schoolId} />
      </div>

      {students.length === 0 ? (
        <div className="text-center py-12 text-base-content/60">
          {t("empty")}
        </div>
      ) : (
        <StudentTable students={students} schoolId={schoolId} />
      )}
    </div>
  );
}
```

## Loading Skeleton (loading.tsx)

```typescript
export default function Loading() {
  return (
    <div className="flex flex-col gap-6">
      <div className="flex items-center justify-between">
        <div className="skeleton h-8 w-32" />
        <div className="skeleton h-10 w-36" />
      </div>
      <div className="overflow-x-auto">
        <table className="table">
          <thead>
            <tr>
              <th><div className="skeleton h-4 w-24" /></th>
              <th><div className="skeleton h-4 w-32" /></th>
              <th><div className="skeleton h-4 w-20" /></th>
            </tr>
          </thead>
          <tbody>
            {Array.from({ length: 5 }).map((_, i) => (
              <tr key={i}>
                <td><div className="skeleton h-4 w-full" /></td>
                <td><div className="skeleton h-4 w-full" /></td>
                <td><div className="skeleton h-4 w-16" /></td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </div>
  );
}
```

## Client Table Component (_components/student-table.tsx)

```typescript
"use client";

import { useTranslations } from "next-intl";
import { useNotification } from "@/hooks/use-notification";
import { deleteStudent } from "@/actions/student";
import { Pencil, Trash2 } from "lucide-react";

interface Student {
  id: string;
  name: string;
  email: string;
  status: string;
}

export function StudentTable({
  students,
  schoolId,
}: {
  students: Student[];
  schoolId: string;
}) {
  const t = useTranslations("students");
  const { notify } = useNotification();

  async function handleDelete(studentId: string) {
    const result = await deleteStudent(schoolId, studentId);
    if (result.success) {
      notify({ type: "success", message: t("messages.deleteSuccess") });
    } else {
      notify({ type: "error", message: result.error });
    }
  }

  return (
    <div className="overflow-x-auto">
      <table className="table">
        <thead>
          <tr>
            <th>{t("table.name")}</th>
            <th>{t("table.email")}</th>
            <th>{t("table.status")}</th>
            <th>{t("table.actions")}</th>
          </tr>
        </thead>
        <tbody>
          {students.map((student) => (
            <tr key={student.id}>
              <td>{student.name}</td>
              <td>{student.email}</td>
              <td>
                <span className="badge badge-sm">{student.status}</span>
              </td>
              <td className="flex gap-1">
                <button className="btn btn-ghost btn-xs">
                  <Pencil className="h-4 w-4" />
                </button>
                <button
                  className="btn btn-ghost btn-xs text-error"
                  onClick={() => handleDelete(student.id)}
                >
                  <Trash2 className="h-4 w-4" />
                </button>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}
```
