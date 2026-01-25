---
description: Start working on an issue - fetches details and moves to In Progress
argument-hint: <issue-id-or-url>
allowed-tools:
  - mcp__plugin_linear_linear__get_issue
  - mcp__plugin_linear_linear__list_issue_statuses
  - mcp__plugin_linear_linear__update_issue
  - mcp__atlassian_jira__get_issue
  - mcp__atlassian_jira__get_transitions
  - mcp__atlassian_jira__transition_issue
---

# /issue-work Command

Start working on an issue by fetching its details and transitioning it to "In Progress" state.

## RFC2119 Keywords

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC2119.

## Instructions

### Step 1: Parse Issue Reference and Detect Provider

You MUST parse the issue reference from the argument:

**URL Detection (Highest Priority):**
- Linear URL: `https://linear.app/team/issue/CIR-123/title` → Provider: Linear, ID: `CIR-123`
- JIRA URL: `https://mycompany.atlassian.net/browse/PROJ-456` → Provider: JIRA, ID: `PROJ-456`

**ID-Only Detection:**
- Pattern: `[A-Z]{2,10}-\d+`
- If provider is ambiguous, check conversation context or ask user

If no argument is provided, you MUST ask the user to provide an issue ID or URL.

### Step 2: Check MCP Availability

You MUST verify the required MCP is available before proceeding:

- **Linear**: Check if `mcp__plugin_linear_linear__get_issue` is accessible
- **JIRA**: Check if `mcp__atlassian_jira__get_issue` is accessible

If MCP is NOT available:
```
The [Provider] MCP is not configured. To use issue tracking:
1. Install the official plugin: `claude plugin install [provider]@claude-plugins-official`
2. Authenticate when prompted

Would you like to continue without issue tracking?
```

You MUST NOT proceed with issue operations if MCP is unavailable.

### Step 3: Fetch Issue Details

#### For Linear:
```json
{
  "id": "CIR-123"
}
```
Extract the team ID from the response.

#### For JIRA:
```json
{
  "issueKey": "PROJ-456"
}
```

### Step 4: Get Available States/Transitions

#### For Linear:
Call `mcp__plugin_linear_linear__list_issue_statuses`:
```json
{
  "team": "<team-id-from-issue>"
}
```
Find a state with type "started" or name containing "In Progress".

#### For JIRA:
Call `mcp__atlassian_jira__get_transitions`:
```json
{
  "issueKey": "PROJ-456"
}
```
Find a transition to "In Progress" or similar active state.

### Step 5: Update Issue State

#### For Linear:
```json
{
  "id": "<issue-uuid>",
  "state": "<in-progress-state-name>"
}
```

#### For JIRA:
```json
{
  "issueKey": "PROJ-456",
  "transitionId": "<in-progress-transition-id>"
}
```

You SHOULD only update if the issue is not already in that state.

### Step 6: Display Issue Context

You MUST display to the user:
- Title
- Description (truncated if longer than 500 characters)
- Labels/Components
- Previous state → New state
- Assignee
- Provider name

### Step 7: Track Active Issue

You MUST output: `Active issue: [ISSUE-ID] ([Provider])`

This enables subsequent `/issue-update` and `/issue-done` commands to reference this issue.

## Example Output

```
Working on CIR-123: Implement calendar sync feature (Linear)

Description: Add ability to sync events with Google Calendar...

Labels: feature, calendar
State: In Progress (was: Backlog)
Assignee: Thomas Indrias

Active issue: CIR-123 (Linear)
```

## Error Handling

- If no argument provided: Ask user to provide an issue ID or URL
- If issue not found: Report the error clearly
- If state transition fails: Report error and show current state
- If MCP unavailable: Inform user and offer to continue without tracking
