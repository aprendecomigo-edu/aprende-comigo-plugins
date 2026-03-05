# Workflow Stages — Detailed Reference

This document provides detailed per-stage instructions for the `subagent-dev` skill. Refer here when you need specifics on what to ask, how to format documents passed to agents, or how to interpret results.

---

## Stage 1: Spec Clarification

### What to ask

Focus only on information that is genuinely missing and would change the implementation. Do not ask about things you can discover from the codebase.

Questions worth asking:
- What exact behaviour is expected? Walk through the happy path and at least one failure case.
- Who are the affected users and roles?
- Are there acceptance criteria the user has in mind?
- Is there a related issue, PR, design doc, or mockup to reference?
- Are there explicit constraints (scope, backward compat, performance, deadlines)?

Questions to avoid:
- "Should I use TypeScript?" (read the codebase)
- "What database are you using?" (read `CLAUDE.md`)
- "How should errors be handled?" (read existing patterns)

### When to stop asking

Stop when you can write a complete spec with: a clear goal, concrete requirements, explicit constraints, and testable acceptance criteria. If only minor details are missing, make a reasonable assumption, state it in the spec, and proceed.

### Spec format

Produce a written spec document to pass to every agent. Keep it concise:

```
## Spec: <Feature or fix name>

**Goal**: <One sentence>

**Requirements**:
- <Requirement 1>
- <Requirement 2>

**Constraints**:
- <Constraint 1 (branch policy, scope limits, etc.)>

**Acceptance criteria**:
- [ ] <Testable criterion 1>
- [ ] <Testable criterion 2>

**Context**:
- Related issue/PR: <link or "none">
- Key files: <list if known>
- Notes: <any clarifications or assumptions made>
```

---

## Stage 2: Implementer Dispatch

### What context to pass

When dispatching the implementer agent, include:
1. The full spec document (from Stage 1)
2. Current branch name (or instruction to create a feature branch)
3. Any specific files you know are relevant
4. Any "do not touch" constraints (e.g., "leave the auth layer unchanged")

### What the implementer does

The implementer will:
1. Ask any remaining clarifying questions (before writing any code)
2. Read `CLAUDE.md` for project conventions
3. Explore the codebase as needed
4. Implement the feature or fix
5. Write tests and run them
6. Commit on a feature branch with a descriptive message
7. Perform a structured self-review:
   - Does the implementation satisfy every acceptance criterion?
   - Is there any unnecessary code (YAGNI)?
   - Do tests cover the critical paths?
   - Are there security or data integrity concerns?

### Evaluating the implementer's return

The implementer should return: what was implemented, what was tested, the commit reference, and the self-review results. Read the summary but treat it as unverified — the spec-reviewer reads the code independently.

If the implementer says it couldn't complete due to a blocker, surface the blocker to the user before retrying.

---

## Stage 3: Spec Review

### Dispatch instructions

Tell the spec-reviewer:
- The full spec document (identical to what the implementer received)
- The branch name or commit hash to review
- To read the code directly — never trust any summary or description from the implementer

### What counts as a pass

The spec-reviewer passes (`✅ Spec compliant`) when:
- Every acceptance criterion in the spec is verifiably satisfied
- No requirements are missing or partially implemented
- No extra unasked-for work was introduced that contradicts requirements

The spec-reviewer fails (`❌ Issues found`) when:
- One or more acceptance criteria are not met
- A requirement was misunderstood or inverted
- Scope creep introduced breaking changes not in the spec

### Handling failures

On failure:
1. Append the reviewer's issue list to the spec document as a new section: `## Spec Review Feedback (Attempt N)`
2. Re-dispatch the implementer with the updated spec
3. Increment retry counter

After **2 retries** with continued failures, stop and present to the user:
```
Spec review failed after 2 attempts. Remaining issues:
[issue list]

Would you like to adjust the spec or requirements?
```

---

## Stage 4: Quality Review

### When to dispatch

Only after spec review returns `✅ Spec compliant`. Do not dispatch earlier.

### Dispatch instructions

Tell the code-quality-reviewer:
- The branch name or commit hash
- That spec compliance has already been verified
- To focus on code quality, not functional correctness

### Interpreting the issue categories

**Critical**: Correctness bugs, security vulnerabilities, data integrity risks, or broken tests. Must fix before opening the PR. Re-dispatch the implementer.

**Important**: Missing test coverage for critical paths, significant maintainability issues, dead code, unclear logic in non-obvious areas. Present to user — most should be fixed now.

**Minor**: Naming improvements, minor style inconsistencies, small optimisations, nice-to-have comments. Present to user — typically deferred unless quick to fix.

### What to do with results

For Critical issues: re-dispatch the implementer with a targeted fix spec. No need for full spec review again — a focused patch review from the spec-reviewer is sufficient.

For Important/Minor issues: present the full list to the user. Ask whether to address them now or track them as follow-up tasks.

---

## Stage 5: Summary and Commit Verification

### Verifying the commit

Before presenting the final summary, verify:
- The feature branch exists and has commits beyond the base branch
- The most recent commit message is descriptive (not "wip" or "fix")
- Tests pass (if CI is accessible, note the status)

### Open PR against staging

Use the `create-pr` skill (from the git plugin) to push the branch and open a PR against `staging`. Pass:
- The branch name
- A summary of what was built (drawn from the spec goal and the implementer's return)
- Any Important/Minor quality issues to note in the PR description

### Presenting the summary

After the PR is open, present a final summary to the user:
- PR link
- Branch and commit reference
- What was built (1-3 sentences, no jargon)
- Spec review result
- Quality review issue counts by category
- Any deferred Important/Minor issues to track as follow-up tasks
