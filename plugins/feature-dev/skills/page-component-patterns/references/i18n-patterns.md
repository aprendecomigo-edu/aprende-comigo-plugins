# Internationalization Patterns

## Translation Key Conventions

Organize keys by feature namespace:

```json
{
  "students": {
    "title": "Students",
    "form": {
      "name": "Full name",
      "email": "Email address",
      "submit": "Save student",
      "cancel": "Cancel"
    },
    "table": {
      "name": "Name",
      "email": "Email",
      "status": "Status",
      "actions": "Actions"
    },
    "messages": {
      "createSuccess": "Student created successfully",
      "updateSuccess": "Student updated successfully",
      "deleteSuccess": "Student deleted successfully",
      "deleteConfirm": "Are you sure you want to delete this student?"
    },
    "empty": "No students found",
    "loading": "Loading students..."
  }
}
```

## Key Naming Rules

- Use dot notation for nesting: `students.form.name`
- Group by UI context: `form`, `table`, `messages`, `errors`
- Use descriptive action names: `createSuccess`, `deleteConfirm`
- Keep keys in both `en.json` and `pt.json` in sync

## Locale-Specific Formatting

Use next-intl formatters for dates, numbers, and currencies:

```typescript
const format = useFormatter();

// Dates
format.dateTime(date, { dateStyle: "medium" });

// Numbers
format.number(amount, { style: "currency", currency: "EUR" });
```

## Supported Locales

- `en` — English (UK)
- `pt` — Portuguese (PT)

Default locale is configured in the project's i18n settings.
