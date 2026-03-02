# Table with Relations Example

## Schema Definition

```typescript
import { pgTable, uuid, varchar, timestamp, index, uniqueIndex } from "drizzle-orm/pg-core";
import { relations } from "drizzle-orm";
import { schools } from "./schools";
import { teachers } from "./teachers";
import { students } from "./students";

// Table definition
export const classes = pgTable(
  "classes",
  {
    id: uuid("id").primaryKey().defaultRandom(),
    schoolId: uuid("school_id")
      .notNull()
      .references(() => schools.id, { onDelete: "cascade" }),
    teacherId: uuid("teacher_id")
      .notNull()
      .references(() => teachers.id, { onDelete: "restrict" }),
    name: varchar("name", { length: 255 }).notNull(),
    subject: varchar("subject", { length: 255 }).notNull(),
    createdAt: timestamp("created_at").defaultNow().notNull(),
    updatedAt: timestamp("updated_at").defaultNow().notNull(),
  },
  (table) => [
    index("classes_school_id_idx").on(table.schoolId),
    index("classes_teacher_id_idx").on(table.teacherId),
    uniqueIndex("classes_school_name_idx").on(table.schoolId, table.name),
  ]
);

// Relations
export const classesRelations = relations(classes, ({ one, many }) => ({
  school: one(schools, {
    fields: [classes.schoolId],
    references: [schools.id],
  }),
  teacher: one(teachers, {
    fields: [classes.teacherId],
    references: [teachers.id],
  }),
  enrollments: many(enrollments),
}));
```

## Zod Validation Schema

```typescript
import { z } from "zod";

export const createClassSchema = z.object({
  name: z.string().min(1, "Class name is required").max(255),
  subject: z.string().min(1, "Subject is required").max(255),
  teacherId: z.string().uuid("Invalid teacher ID"),
});

export const updateClassSchema = createClassSchema.partial();
```

## Usage in Query

```typescript
// Eager load with relations
const classWithDetails = await db.query.classes.findFirst({
  where: and(eq(classes.schoolId, schoolId), eq(classes.id, classId)),
  with: {
    teacher: true,
    enrollments: {
      with: { student: true },
    },
  },
});
```
