# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**issue-dev** is a Claude Code Plugin that manages issue/ticket states across project management providers (Linear, JIRA) directly from the terminal. It provides slash commands, reusable skills, and an autonomous agent for workflow automation.

## Architecture

```
.claude-plugin/
├── plugin.json          # Plugin manifest (name, version, component paths)
├── marketplace.json     # Marketplace distribution manifest
├── commands/            # User-invocable slash commands (/issue-work, /issue-update, /issue-done)
├── skills/              # Reusable guidance components
│   ├── issue-workflow/  # State transition logic
│   └── provider-detection/  # Provider identification from URLs/IDs
└── agents/              # Autonomous issue detection agent
```

**Design Pattern**: Provider-agnostic adapter with graceful degradation. Detection layer parses issue references → capability check verifies MCP availability → workflow layer executes state transitions → context tracking maintains active issue.

## Plugin Components

### Commands (`.claude-plugin/commands/`)
- `issue-work.md`: Start working on an issue (fetches details, transitions to In Progress)
- `issue-update.md`: Add progress comments to active issue
- `issue-done.md`: Complete work (transitions to Done, clears tracking)

### Skills (`.claude-plugin/skills/`)
- `issue-workflow/SKILL.md`: Issue state transition guidance
- `provider-detection/SKILL.md`: URL/ID pattern matching for Linear and JIRA

### Agent (`.claude-plugin/agents/`)
- `issue-dev.md`: Proactively detects issue references in user messages

## MCP Dependencies

| Provider | MCP Required | Key Tools |
|----------|--------------|-----------|
| Linear | `linear@claude-plugins-official` | `get_issue`, `update_issue`, `create_comment`, `list_issue_statuses` |
| JIRA | Atlassian MCP | `get_issue`, `transition_issue`, `add_comment`, `get_transitions` |

All operations check MCP availability first and gracefully skip if unavailable.

## Development

No build system required. Plugin uses markdown-based configuration with YAML frontmatter.

**Testing changes locally**:
```bash
# Install from local path for development
claude plugin install ./.claude-plugin
```

**Plugin manifest validation**: Claude Code validates `plugin.json` structure on installation.

## Conventions

- RFC2119 keywords (MUST, SHOULD, MAY) used throughout documentation
- Issue ID patterns: `[A-Z]{2,10}-\d+` (e.g., CIR-123, PROJ-456)
- Active issue tracking output format: `Active issue: [ID] ([Provider])`
- Commands include `allowed-tools` frontmatter specifying required MCP tools
