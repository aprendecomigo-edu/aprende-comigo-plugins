# Aprende Comigo Plugins

Bespoke Claude Code plugins for the [Aprende Comigo](https://github.com/aprendecomigo-edu) educational platform.


## Plugins

| Plugin | Description |
|--------|-------------|
| [dev-workflow](custom/dev-workflow/) | Lightweight subagent-driven feature development and bug fixing workflow: implement, spec review, and quality review via sequential fresh subagents |
| [neon](custom/neon/) | Neon Serverless Postgres integration with MCP tools, Drizzle ORM skill, and a database workflow agent |
| [pr-review-toolkit](custom/pr-review-toolkit/) | PR review agents for comments, tests, error handling, type design, and code quality |
| [git](custom/git/) | Git workflows, worktree management, and GitHub project board integration with MCP server and specialised agents |
| [browser-control](custom/browser-control/) | Browser automation via Anthropic's playwright-cli: navigate pages, fill forms, take screenshots, mock network requests, manage sessions, and generate Playwright tests |

## Installation

Install plugins via Claude Code's plugin system:

```
/plugin install {plugin-name}@aprende-comigo-plugins
```

Or browse in `/plugin > Discover`.

## Structure

```
custom/                          # All bespoke plugins
  dev-workflow/                  # Subagent-driven feature dev & bug-fix workflow
  neon/                          # Neon Postgres + Drizzle ORM
  pr-review-toolkit/             # PR review agents
  git/                           # Git workflows + GitHub MCP
  browser-control/               # Browser automation via playwright-cli
.claude-plugin/marketplace.json  # Marketplace manifest
```

Each plugin follows the standard Claude Code plugin layout:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json      # Plugin metadata (required)
├── .mcp.json            # MCP server configuration (optional)
├── commands/            # Slash commands (optional)
├── agents/              # Agent definitions (optional)
├── skills/              # Skill definitions (optional)
├── LICENSE              # Apache 2.0
└── README.md            # Documentation
```

## License

All plugins are licensed under Apache 2.0. See each plugin's LICENSE file for details.
