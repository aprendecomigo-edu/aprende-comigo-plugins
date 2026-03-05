# Git Plugin

Git workflows, worktree management, and GitHub project board integration for Claude Code.

## Features

- **Git Expert Agent** (Haiku): Worktree setup, branch management, and general git operations
- **Project Manager Agent** (Sonnet): GitHub Projects board transitions and status queries
- **Project Board Workflow**: Defined transitions for the Aprende Comigo development lifecycle (Backlog -> In Progress -> In Review -> Done)

## Prerequisites

### Project Board Operations

The `gh` CLI must have the `read:project` scope:

```bash
gh auth refresh -h github.com -s read:project
```

Required CLI tools:
- `gh` (GitHub CLI)
- `jq` (JSON processing)
- Git 2.5+ (for worktree support)

## Installation

```bash
claude plugin add --source ./custom/git
```

## Components

### Agent: `git-expert`

Lightweight agent (Haiku) for git operations:
- Worktree creation with safety verification
- Branch management (create, rebase, merge)
- General git workflow assistance

### Agent: `project-manager`

Board management agent (Sonnet) for GitHub Projects:
- Start work on issues (Backlog -> In Progress)
- Post-merge handoff (In Progress -> In Review, reassign to PM)
- Board status queries and backlog triage

### Skill: `using-git-worktrees`

Isolated git worktree creation with smart directory selection and automatic project setup.

### Skill: `gh-cli`

Comprehensive GitHub CLI (gh) reference covering repositories, issues, PRs, Actions, projects, releases, gists, codespaces, organizations, and extensions.

### Skill: `create-pr`

Push the current feature branch and open a pull request against `staging`. Generates a title and description from commits and any provided spec context, then returns the PR URL.

### Skill: `ac-project-workflow`

Aprende Comigo project board workflow rules, team roles, and transition logic.

## Usage

### Git worktrees

```
Set up a worktree for the auth feature
```

### Check board status

```
Show me the current board status
```

### Start working on an issue

```
Start work on issue #452
```

### After merging a PR to staging

```
I just merged PR #456 to staging, update the board for issue #123
```

## Source Attribution

### using-git-worktrees skill

| Field | Value |
|-------|-------|
| **Source Repository** | [obra/superpowers](https://github.com/obra/superpowers) |
| **Original Author** | Jesse Vincent |
| **License** | MIT |
| **Copied From** | https://github.com/obra/superpowers/blob/main/skills/using-git-worktrees/SKILL.md |
| **Date Copied** | 2026-03-05 |

### gh-cli skill

| Field | Value |
|-------|-------|
| **Source Repository** | [github/awesome-copilot](https://github.com/github/awesome-copilot) |
| **Original Author** | GitHub, Inc. |
| **License** | MIT |
| **Copied From** | https://github.com/github/awesome-copilot/blob/main/skills/gh-cli/SKILL.md |
| **Date Copied** | 2026-03-05 |

## License

Apache 2.0 (plugin), MIT (using-git-worktrees, gh-cli skills)
