# Page & Component Patterns

Third-person description: Guides the developer through creating Next.js App Router pages, DaisyUI v5 components, next-intl internationalization, form handling with Zod validation, and notification patterns for a multi-tenant educational SaaS. Activates when the developer needs to "add a new page", "create a form", "add a UI component", "build a dashboard", or "create a list view".

---

## App Router Page Structure

Pages follow the Next.js App Router conventions with locale-based routing:

```
app/[locale]/(authenticated)/[schoolId]/feature-name/
├── page.tsx          # Main page component (server component by default)
├── loading.tsx       # Loading skeleton
├── error.tsx         # Error boundary
├── layout.tsx        # Optional layout wrapper
└── _components/      # Page-specific client components
```

### Server vs Client Components

- **Server components** (default) — data fetching, authorization checks, initial rendering
- **Client components** (`"use client"`) — interactivity, forms, state, event handlers
- Keep client components small and focused. Fetch data in server components, pass as props.

## DaisyUI v5 Components

Use DaisyUI v5 component classes for consistent UI:

- **Layout**: `card`, `drawer`, `navbar`, `footer`
- **Data display**: `table`, `stat`, `badge`, `avatar`
- **Forms**: `input`, `select`, `textarea`, `checkbox`, `toggle`
- **Actions**: `btn`, `btn-primary`, `btn-ghost`, `btn-error`
- **Feedback**: `alert`, `toast`, `loading`, `skeleton`
- **Navigation**: `tabs`, `breadcrumbs`, `pagination`

Combine with Tailwind utility classes for spacing, sizing, and responsive design. Do not write custom CSS — compose with existing classes.

## Internationalization with next-intl

All user-facing text must use translation keys:

### In Server Components

```typescript
import { getTranslations } from "next-intl/server";

export default async function Page() {
  const t = await getTranslations("students");
  return <h1>{t("title")}</h1>;
}
```

### In Client Components

```typescript
"use client";
import { useTranslations } from "next-intl";

export function StudentForm() {
  const t = useTranslations("students");
  return <label>{t("form.name")}</label>;
}
```

### Translation Files

Add keys to both language files in `messages/`:

- `messages/en.json` — English (UK)
- `messages/pt.json` — Portuguese (PT)

## Form Handling

Forms combine server actions with client-side validation:

1. **Define Zod schema** in `lib/schemas/` — shared between client and server
2. **Client-side validation** — validate on submit before calling the action
3. **Server action** — validates again server-side (never trust client)
4. **Feedback** — use `useNotification` hook to show success/error messages

## useNotification Pattern

Provide user feedback after actions:

```typescript
const { notify } = useNotification();

async function handleSubmit(formData: FormData) {
  const result = await createStudent(schoolId, formData);
  if (result.success) {
    notify({ type: "success", message: t("createSuccess") });
  } else {
    notify({ type: "error", message: result.error });
  }
}
```

## Responsive Design

- Use Tailwind responsive prefixes: `sm:`, `md:`, `lg:`, `xl:`
- Mobile-first approach — default styles for mobile, add breakpoints for larger screens
- Tables: consider card layout on mobile, table on desktop
- Navigation: drawer on mobile, sidebar on desktop

## Loading & Error States

Every page should handle loading and error states:

- `loading.tsx` — skeleton UI matching the page layout
- `error.tsx` — user-friendly error message with retry option
- Use DaisyUI's `skeleton` class for loading placeholders
- Use `loading loading-spinner` for inline loading indicators

## References

- [i18n-patterns.md](references/i18n-patterns.md) — translation key conventions and locale patterns
- [form-patterns.md](references/form-patterns.md) — form structure, validation, and submission patterns

## Examples

- [crud-page.md](examples/crud-page.md) — complete CRUD page with list, create, and edit views
