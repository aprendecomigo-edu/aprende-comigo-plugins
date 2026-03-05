---
description: >
  This skill should be used when the user has a spec or requirements for a multi-step task and needs an implementation plan before touching code. Trigger phrases: "write a plan for...", "plan this feature", "create an implementation plan", "before we start, plan out...", "break this down into tasks".
---

# Writing Plans

## Overview

Write a comprehensive implementation plan assuming the engineer has zero context for the codebase and questionable taste. Document everything they need to know: which files to touch for each task, exact code, testing commands, docs to check, and how to verify each step. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. Frequent commits.

Assume a skilled developer who knows almost nothing about the toolset or problem domain — and doesn't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## Bite-Sized Task Granularity

Each step is one action (2–5 minutes):
- "Write the failing test" — step
- "Run it to make sure it fails" — step
- "Implement the minimal code to make the test pass" — step
- "Run the tests and make sure they pass" — step
- "Commit" — step

## Plan Document Header

Every plan MUST start with this header:

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** Use the `subagent-dev` skill to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.ts`
- Modify: `exact/path/to/existing.ts:123-145`
- Test: `tests/exact/path/to/test.ts`

**Step 1: Write the failing test**

```typescript
it('should do specific behaviour', () => {
  const result = fn(input)
  expect(result).toEqual(expected)
})
```

**Step 2: Run test to verify it fails**

Run: `pnpm test path/to/test.ts`
Expected: FAIL with "[error message]"

**Step 3: Write minimal implementation**

```typescript
export function fn(input: Input): Output {
  return expected
}
```

**Step 4: Run test to verify it passes**

Run: `pnpm test path/to/test.ts`
Expected: PASS

**Step 5: Commit**

```bash
git add tests/path/test.ts src/path/file.ts
git commit -m "feat: add specific behaviour"
```
````

## Remember

- Exact file paths always
- Complete code in the plan — not "add validation" but the actual validation code
- Exact commands with expected output
- DRY, YAGNI, TDD, frequent commits
- Check `CLAUDE.md` before writing tasks — honour established patterns for auth, Drizzle, i18n, error handling

## Execution Handoff

After saving the plan, offer the execution choice:

> **Plan saved to `docs/plans/<filename>.md`. Two execution options:**
>
> **1. Subagent-Driven (this session)** — dispatch a fresh implementer subagent per task, spec and quality review between tasks, fast iteration. Uses the `subagent-dev` skill.
>
> **2. Later / separate session** — open a new session in the worktree, load the plan, and work through it task-by-task manually.
>
> Which approach?

If **Subagent-Driven** is chosen, use the `subagent-dev` skill: treat each task in the plan as its own spec and dispatch the implementer → spec-reviewer → quality-reviewer pipeline per task.
