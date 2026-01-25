---
description: Add a comment or update the current issue
argument-hint: [comment-text]
allowed-tools:
  - mcp__plugin_linear_linear__get_issue
  - mcp__plugin_linear_linear__update_issue
  - mcp__plugin_linear_linear__create_comment
  - mcp__atlassian_jira__get_issue
  - mcp__atlassian_jira__add_comment
  - mcp__atlassian_jira__update_issue
---

# /issue-update Command

Add a progress comment or update details on the currently active issue.

## RFC2119 Keywords

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC2119.

## Instructions

### Step 1: Identify Active Issue

You MUST look for "Active issue: [ID] ([Provider])" mentioned earlier in the conversation.

If no active issue is found, you MUST ask the user to either:
- Run `/issue-work [issue-id-or-url]` first, OR
- Provide an issue ID or URL directly

### Step 2: Verify MCP Availability

You MUST verify the provider's MCP is still available.

If MCP is NOT available:
```
The [Provider] MCP is not configured. Cannot update issue.
```

### Step 3: Handle Comment (If Argument Provided)

If the user provides argument text, add a comment:

#### For Linear:
```json
{
  "issueId": "<issue-uuid>",
  "body": "<argument-text>"
}
```

#### For JIRA:
```json
{
  "issueKey": "PROJ-456",
  "body": "<argument-text>"
}
```

You MUST confirm the comment was added successfully.

### Step 4: Handle No Argument

If no argument is provided, you MUST ask the user what they want to update:
- Add a comment
- Update description
- Add/remove labels

Then execute the appropriate action based on their response.

### Step 5: Description Updates

For description updates:

#### For Linear:
1. Fetch current issue with `mcp__plugin_linear_linear__get_issue`
2. Update with `mcp__plugin_linear_linear__update_issue`:
```json
{
  "id": "<issue-uuid>",
  "description": "<new-description-in-markdown>"
}
```

#### For JIRA:
1. Fetch current issue with `mcp__atlassian_jira__get_issue`
2. Update with `mcp__atlassian_jira__update_issue`:
```json
{
  "issueKey": "PROJ-456",
  "fields": {
    "description": "<new-description>"
  }
}
```

## Example Usage

```
/issue-update Fixed the auth token refresh issue, moving on to calendar sync
```

## Example Output

```
Added comment to CIR-123 (Linear):
> Fixed the auth token refresh issue, moving on to calendar sync
```

## Error Handling

- If no active issue and no issue specified: Prompt user to use `/issue-work` first
- If comment creation fails: Report error with details from API response
- If MCP unavailable: Inform user and suggest reconfiguring
