---
name: provider-detection
description: Provides guidance on detecting project management providers from URLs and issue IDs. Use when parsing issue references to determine which provider (Linear, JIRA, etc.) to use.
---

# Provider Detection

This skill guides you through detecting project management providers from issue references.

## RFC2119 Keywords

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC2119.

## Supported Providers

| Provider | URL Pattern | Issue ID Pattern | MCP Tools Prefix |
|----------|-------------|------------------|------------------|
| Linear | `https://linear.app/*/issue/*` | `[A-Z]{2,5}-\d+` | `mcp__plugin_linear_linear__` |
| JIRA | `https://*.atlassian.net/browse/*` | `[A-Z]{2,10}-\d+` | `mcp__atlassian_jira__` |

## Detection Priority

You MUST detect providers in this order:

1. **URL-based detection** (highest confidence)
   - Linear: URL contains `linear.app`
   - JIRA: URL contains `atlassian.net/browse` or `.jira.com`

2. **Context-based detection** (if no URL)
   - Check if user mentioned provider name earlier in conversation
   - Check for active issue context from previous commands

3. **ID-only detection** (lowest confidence)
   - Issue ID pattern `[A-Z]{2,5}-\d+` could be Linear OR JIRA
   - You SHOULD ask user to clarify if provider is ambiguous

## MCP Availability Check

Before performing any operation, you MUST verify the required MCP is available:

### For Linear
Check if tools starting with `mcp__plugin_linear_linear__` are accessible.

### For JIRA
Check if tools starting with `mcp__atlassian_jira__` are accessible.

## Handling Missing MCP

If the required MCP is not available:

1. You MUST inform the user clearly:
   ```
   The [Provider] MCP is not configured. To use issue tracking with [Provider]:
   1. Install the official [Provider] plugin: `claude plugin install [provider]@claude-plugins-official`
   2. Authenticate when prompted
   ```

2. You MUST NOT attempt to call unavailable MCP tools
3. You SHOULD offer to continue without issue tracking

## Provider-Specific Tool Mappings

### Linear MCP Tools
- Get issue: `mcp__plugin_linear_linear__get_issue`
- List statuses: `mcp__plugin_linear_linear__list_issue_statuses`
- Update issue: `mcp__plugin_linear_linear__update_issue`
- Create comment: `mcp__plugin_linear_linear__create_comment`

### JIRA MCP Tools (when available)
- Get issue: `mcp__atlassian_jira__get_issue`
- Get transitions: `mcp__atlassian_jira__get_transitions`
- Transition issue: `mcp__atlassian_jira__transition_issue`
- Add comment: `mcp__atlassian_jira__add_comment`

## Output Format

When provider is detected, you MUST output:
```
Provider: [Linear|JIRA|Unknown]
Issue ID: [ID]
MCP Available: [Yes|No]
```
