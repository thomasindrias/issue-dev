# issue-dev

Manage issue/ticket states across Linear, JIRA, and other project management providers directly from Claude Code.

## How It Works

Start working on an issue, add progress updates, and mark it done—all without leaving your terminal. The plugin automatically detects your provider from URLs or issue IDs and handles state transitions through MCP integrations.

```
/issue-work CIR-123           # Fetch issue, move to In Progress
... do your work ...
/issue-update Fixed auth bug  # Add progress comment
/issue-done PR merged         # Move to Done, add completion note
```

## Installation

```bash
claude plugin install thomasindrias/issue-dev
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

### `/issue-work <issue-id-or-url>`

Start working on an issue. Fetches details and moves to "In Progress" state.

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

### `/issue-update [comment]`

Add a progress comment or update the current issue.

```
/issue-update Fixed the auth token refresh issue, moving on to calendar sync
```

**Output:**
```
Added comment to CIR-123 (Linear):
> Fixed the auth token refresh issue, moving on to calendar sync
```

### `/issue-done [comment]`

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
├── commands/
│   ├── issue-work.md      # Start working on an issue
│   ├── issue-update.md    # Add progress updates
│   └── issue-done.md      # Complete work on an issue
├── skills/
│   ├── issue-workflow/    # Workflow management guidance
│   └── provider-detection/# Provider detection logic
└── agents/
    └── issue-dev.md       # Autonomous issue management agent
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
