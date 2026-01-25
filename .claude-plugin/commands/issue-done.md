---
description: Complete work on the current issue - moves to Done state
argument-hint: [completion-comment]
allowed-tools:
  - mcp__plugin_linear_linear__get_issue
  - mcp__plugin_linear_linear__list_issue_statuses
  - mcp__plugin_linear_linear__update_issue
  - mcp__plugin_linear_linear__create_comment
  - mcp__atlassian_jira__get_issue
  - mcp__atlassian_jira__get_transitions
  - mcp__atlassian_jira__transition_issue
  - mcp__atlassian_jira__add_comment
---

# /issue-done Command

Complete work on the current issue by transitioning it to "Done" state.

## RFC2119 Keywords

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC2119.

## Instructions

### Step 1: Identify Active Issue

You MUST look for "Active issue: [ID] ([Provider])" mentioned earlier in the conversation.

If no active issue is found, you MUST ask the user to provide an issue ID or URL.

### Step 2: Verify MCP Availability

You MUST verify the provider's MCP is available.

If MCP is NOT available:
```
The [Provider] MCP is not configured. Cannot complete issue.
Would you like me to just clear the active issue tracking?
```

### Step 3: Fetch Issue Details

#### For Linear:
```json
{
  "id": "<issue-id>"
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
Find a state with type "completed" or name containing "Done", "Complete", or "Closed".

#### For JIRA:
Call `mcp__atlassian_jira__get_transitions`:
```json
{
  "issueKey": "PROJ-456"
}
```
Find a transition to "Done", "Closed", or "Resolved".

### Step 5: Add Completion Comment (If Argument Provided)

If the user provides a completion comment:

#### For Linear:
```json
{
  "issueId": "<issue-uuid>",
  "body": "<completion-comment>"
}
```

#### For JIRA:
```json
{
  "issueKey": "PROJ-456",
  "body": "<completion-comment>"
}
```

### Step 6: Update Issue State

#### For Linear:
```json
{
  "id": "<issue-uuid>",
  "state": "<done-state-name>"
}
```

#### For JIRA:
```json
{
  "issueKey": "PROJ-456",
  "transitionId": "<done-transition-id>"
}
```

You MUST confirm the state transition to the user.

### Step 7: Clear Active Issue

You MUST output: `Completed issue: [ISSUE-ID] ([Provider]). No active issue.`

This clears the active issue tracking for the session.

## Example Usage

```
/issue-done Implemented and tested calendar sync feature. PR merged.
```

## Example Output

```
Completed CIR-123: Implement calendar sync feature (Linear)

Added completion note:
> Implemented and tested calendar sync feature. PR merged.

State: Done (was: In Progress)

Completed issue: CIR-123 (Linear). No active issue.
```

## Error Handling

- If no active issue and no issue specified: Prompt user to provide an issue ID
- If state transition fails: Report error and show current state
- If already in Done state: Inform user and still add comment if provided
- If MCP unavailable: Offer to clear active issue tracking only
