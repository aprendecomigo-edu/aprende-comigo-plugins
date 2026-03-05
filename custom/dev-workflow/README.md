# dev-workflow

Lightweight subagent-driven feature development and bug fixing workflow plugin for Claude Code.

## Overview

`dev-workflow` provides a `subagent-dev` skill that triggers automatically when you ask Claude to implement a feature or fix a bug. Instead of doing all the work in one long context, it dispatches sequential fresh subagents — an implementer, a spec reviewer, and a quality reviewer — each with a clean context. This prevents the implementer's assumptions from contaminating the review.

Domain-specific knowledge (auth, Drizzle, i18n, error handling) stays in your project's `CLAUDE.md`. This plugin provides only the workflow orchestration.

## Features

- **Skill**: `subagent-dev` — auto-triggers on feature/bug requests; orchestrates the full implement → spec review → quality review pipeline
- **Agent**: `implementer` — builds the feature, writes tests, commits, self-reviews
- **Agent**: `spec-reviewer` — independently verifies every spec requirement is met (blind to implementer's claims)
- **Agent**: `code-quality-reviewer` — evaluates code quality, test coverage, and maintainability after spec compliance passes

## Workflow

```
User asks to build a feature or fix a bug
  → Orchestrator clarifies spec with user
  → Implementer: implement, test, commit, self-review
  → Spec reviewer: verify all requirements met (max 2 retries on failure)
  → Quality reviewer: check code quality, coverage, conventions
  → Orchestrator: present final summary to user
```

## Installation

Add this plugin to your Claude Code project via the Aprende Comigo marketplace.

## Prerequisites

- Claude Code with plugin support
- A project with a `CLAUDE.md` file documenting conventions (the agents read this to follow your patterns)

## Usage

Just describe what you want to build:

- "Implement a feature to let teachers set their availability"
- "Fix the bug where payment status isn't updating after a webhook"
- "Build the student import flow from CSV"

The `subagent-dev` skill triggers automatically and the orchestrator will clarify the spec with you before dispatching any agents.

## License

Apache 2.0 — see [LICENSE](./LICENSE)
