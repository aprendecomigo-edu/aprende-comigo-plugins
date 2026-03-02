# Form Patterns

## Form Structure

Forms follow a consistent pattern:

1. **Zod schema** — defines validation rules (shared client/server)
2. **Client component** — renders the form with DaisyUI inputs
3. **Submit handler** — validates client-side, calls server action
4. **Feedback** — shows success/error notification

## Basic Form Template

```typescript
"use client";

import { useTranslations } from "next-intl";
import { useNotification } from "@/hooks/use-notification";
import { createStudentSchema } from "@/lib/schemas/student";
import { createStudent } from "@/actions/student";

export function CreateStudentForm({ schoolId }: { schoolId: string }) {
  const t = useTranslations("students.form");
  const { notify } = useNotification();

  async function handleSubmit(formData: FormData) {
    // Client-side validation
    const raw = Object.fromEntries(formData);
    const result = createStudentSchema.safeParse(raw);
    if (!result.success) {
      notify({ type: "error", message: result.error.issues[0].message });
      return;
    }

    // Server action
    const response = await createStudent(schoolId, formData);
    if (response.success) {
      notify({ type: "success", message: t("createSuccess") });
    } else {
      notify({ type: "error", message: response.error });
    }
  }

  return (
    <form action={handleSubmit} className="flex flex-col gap-4">
      <div className="form-control">
        <label className="label">{t("name")}</label>
        <input name="name" type="text" className="input input-bordered" required />
      </div>
      <div className="form-control">
        <label className="label">{t("email")}</label>
        <input name="email" type="email" className="input input-bordered" required />
      </div>
      <div className="flex gap-2 justify-end">
        <button type="button" className="btn btn-ghost">{t("cancel")}</button>
        <button type="submit" className="btn btn-primary">{t("submit")}</button>
      </div>
    </form>
  );
}
```

## Form Field Patterns

### Text Input
```html
<div className="form-control">
  <label className="label">{t("fieldName")}</label>
  <input name="field" type="text" className="input input-bordered" />
  <span className="label-text-alt text-error">{error}</span>
</div>
```

### Select
```html
<div className="form-control">
  <label className="label">{t("status")}</label>
  <select name="status" className="select select-bordered">
    <option value="active">{t("statusActive")}</option>
    <option value="inactive">{t("statusInactive")}</option>
  </select>
</div>
```

### Confirmation Dialogs

Use DaisyUI modal for destructive actions:

```html
<dialog id="delete-modal" className="modal">
  <div className="modal-box">
    <h3 className="font-bold text-lg">{t("deleteConfirm")}</h3>
    <div className="modal-action">
      <button className="btn btn-ghost">{t("cancel")}</button>
      <button className="btn btn-error" onClick={handleDelete}>{t("delete")}</button>
    </div>
  </div>
</dialog>
```
