# ui-ux

UI/UX design skills for reviewing and auditing web interfaces.

## Overview

The `ui-ux` plugin provides skills for reviewing UI code against established web interface guidelines covering accessibility, performance, and UX best practices.

## Features

- **web-design-guidelines**: Audits UI code against 100+ rules from the Vercel Web Interface Guidelines, outputting findings in `file:line` format.
- **aprende-design-system**: Map and rules for the aprende comigo. brand and design system — points directly to the live source files (tokens.css, kit.css, design-guidelines HTML, React components) rather than duplicating content. Includes architectural rules not derivable from reading code.

## Installation

Add to your Claude Code plugin configuration.

## Prerequisites

- WebFetch tool access (to retrieve the latest guidelines)

## Usage

Trigger the `web-design-guidelines` skill by asking Claude to:

- "Review my UI"
- "Check accessibility"
- "Audit my design"
- "Review my UX"
- "Check my site against best practices"

Optionally provide a file or glob pattern to scope the audit.
