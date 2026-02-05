# issue-dev

Manage issue/ticket states across Linear, JIRA, and other project management providers directly from Claude Code.

<img width="1500" height="612" alt="image" src="https://github.com/user-attachments/assets/1f1335d9-4fc1-4575-ad48-d38b34b2020a" />

## How It Works

Manage your issues directly from the terminal. The plugin automatically detects your provider from URLs or issue IDs and handles state transitions through MCP integrations.

## Two Workflows

| Aspect | Quick Workflow | Guided Workflow |
|--------|----------------|-----------------|
| **Commands** | `/issue-work` → `/issue-update` → `/issue-done` | `/issue-plan` |
| **Use when** | You know what to do | You need help planning |
| **What it does** | Tracks issue status as you work | Generates complete implementation plan |
| **Planning** | None - just status tracking | Three-pass planning with validation |
| **Quality gates** | None | Typecheck, lint, build, code review |
| **Dependencies** | MCP only | MCP + `superpowers` plugin |

### Quick Workflow

For when you already know what to implement:

```
/issue-work CIR-123           # Fetch issue, move to In Progress
... do your work ...
/issue-update Fixed auth bug  # Add progress comment
/issue-done PR merged         # Move to Done, add completion note
```

### Guided Workflow

For when you need a structured implementation plan:

```
/issue-plan CIR-123           # Generates full implementation workflow
```

The guided workflow analyzes your issue, explores the codebase, and produces a validated implementation plan with quality gates—before you write any code.

## Installation

First, add the marketplace:
```bash
claude plugin marketplace add thomasindrias/issue-dev
```

Then install the plugin:
```bash
claude plugin install issue-dev@issue-dev
```

## Requirements

This plugin requires MCP connections to your project management provider:

### Linear

Install the Linear MCP plugin:
```bash
claude plugin install linear@claude-plugins-official
```

### JIRA

Configure the Atlassian JIRA MCP server in your `.mcp.json`:
```json
{
  "mcpServers": {
    "atlassian": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-server-atlassian"]
    }
  }
}
```

## Commands

### Issue First Development

#### `/issue-plan [issue-url-or-id]`

Generate a complete implementation workflow from an issue. Analyzes requirements, explores your codebase, and produces a validated plan—before you write any code.

```
/issue-plan https://linear.app/myteam/issue/CIR-123/new-feature
/issue-plan PROJ-456
/issue-plan  # Interactive mode - prompts for issue
```

**What it does:**
1. Fetches issue details and moves to "In Progress"
2. Analyzes task type (frontend, backend, full-stack, design)
3. Runs three-pass planning with code review validation
4. Creates development isolation using worktrees
5. Applies quality gates (typecheck, lint, build)

**Requires:** `superpowers` plugin for planning and code review capabilities.

```bash
claude plugin install superpowers@superpowers
```

### Issue Tracking

Simple status management—for when you already know what to build.

#### `/issue-work <issue-id-or-url>`

Track that you're working on an issue. Fetches details and updates status to "In Progress".

```
/issue-work https://linear.app/myteam/issue/CIR-123/feature-title
/issue-work PROJ-456
```

**Output:**
```
Working on CIR-123: Implement calendar sync feature (Linear)

Description: Add ability to sync events with Google Calendar...

Labels: feature, calendar
State: In Progress (was: Backlog)
Assignee: Thomas Indrias

Active issue: CIR-123 (Linear)
```

#### `/issue-update [comment]`

Add a progress comment to the current issue.

```
/issue-update Fixed the auth token refresh issue, moving on to calendar sync
```

**Output:**
```
Added comment to CIR-123 (Linear):
> Fixed the auth token refresh issue, moving on to calendar sync
```

#### `/issue-done [comment]`

Complete work on the current issue. Moves to "Done" state.

```
/issue-done Implemented and tested calendar sync feature. PR merged.
```

**Output:**
```
Completed CIR-123: Implement calendar sync feature (Linear)

Added completion note:
> Implemented and tested calendar sync feature. PR merged.

State: Done (was: In Progress)

Completed issue: CIR-123 (Linear). No active issue.
```

## What's Inside

```
issue-dev/
├── .claude-plugin/
│   ├── plugin.json        # Plugin manifest
│   ├── marketplace.json   # Marketplace manifest
│   ├── commands/
│   │   ├── issue-work.md      # Start working on an issue
│   │   ├── issue-update.md    # Add progress updates
│   │   ├── issue-done.md      # Complete work on an issue
│   │   └── issue-plan.md      # Issue First Development workflow
│   ├── skills/
│   │   ├── issue-workflow/    # Workflow management guidance
│   │   ├── provider-detection/# Provider detection logic
│   │   └── workflow/          # IFD workflow philosophy & standards
│   └── agents/
│       └── issue-dev.md       # Autonomous issue management agent
├── LICENSE
└── README.md
```

## Supported Providers

| Provider | URL Pattern | Issue ID Pattern | MCP Required |
|----------|-------------|------------------|--------------|
| Linear | `https://linear.app/*/issue/*` | `[A-Z]{2,5}-\d+` | `linear@claude-plugins-official` |
| JIRA | `https://*.atlassian.net/browse/*` | `[A-Z]{2,10}-\d+` | Atlassian MCP |

### Provider Detection

The plugin automatically detects providers:

1. **URL-based** (highest priority): Extract provider from Linear or JIRA URLs
2. **Context-based**: Use provider from earlier in the conversation
3. **ID-only**: Pattern matching (may ask for clarification if ambiguous)

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'Add my feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request

## License

MIT - see [LICENSE](LICENSE) for details.

## Author

**Thomas Indrias**

For issues and feature requests, please use [GitHub Issues](https://github.com/thomasindrias/issue-dev/issues).
