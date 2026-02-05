---
name: workflow
description: Use when working on issue-driven development tasks to understand the IFD workflow philosophy, planning protocol, and quality standards. Invoke when users ask about "ifd workflow", "planning iterations", "issue first development", or need guidance on the structured development approach.
---

# Issue First Development Workflow

This skill documents the philosophy and standards behind Issue First Development (IFD) - a structured approach to implementing features from issue tracker tickets.

## RFC 2119 Keywords

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

## Core Philosophy

**Issue First Development** means:
1. You MUST always start with a tracked issue (JIRA, Linear, etc.)
2. You MUST understand requirements fully before writing code
3. You MUST plan thoroughly with multiple iterations
4. You MUST validate the plan before implementation
5. You MUST follow a consistent workflow for quality and traceability

## Planning Protocol

Every implementation task MUST follow this planning protocol:

### Three-Pass Planning

1. **First Pass: Exploration**
   - You MUST read the issue thoroughly
   - You MUST explore the codebase to understand context
   - You MUST identify affected files and components
   - You SHOULD note dependencies and potential impacts

2. **Second Pass: Refinement**
   - You MUST draft an implementation approach
   - You MUST identify gaps in understanding
   - You SHOULD research patterns used in the codebase
   - You MUST refine the approach based on findings

3. **Third Pass: Validation**
   - You MUST run `/superpowers:code-review` on the plan
   - You MUST address any concerns raised
   - You MUST finalize the implementation strategy
   - You MUST NOT proceed until plan is solid

### Why Three Passes?

- First pass often reveals unknown unknowns
- Second pass connects the dots
- Third pass catches issues before they become bugs
- This prevents the "I didn't realize..." problem mid-implementation

## Workflow Standards

### Pre-Implementation

You MUST complete these steps before writing code:

```
> You MUST pull latest main before starting
> You MUST clear the context before starting implementation
> You MUST create a separate worktree and branch to work in
```

**Worktree isolation** prevents:
- Conflicts with ongoing work
- Accidental commits to wrong branch
- Context pollution between tasks

### Task Type Guidance

**Frontend Tasks:**
- You MUST use frontend-design skill for UI work
- You MUST use playwright for verification
- You SHOULD check storybook if available
- You SHOULD verify responsive behavior

**Backend Tasks:**
- You MUST verify via endpoint testing
- You SHOULD check existing tests for patterns
- You SHOULD consider API contract implications
- You MUST validate error handling

### Quality Gates

Before completion, you MUST:
1. Run `/superpowers:code-review`
2. Fix all identified issues
3. Run typecheck, lint, and build
4. Verify changes match acceptance criteria

You MUST NOT mark a task as complete if any quality gate fails.

### Database Changes

If the task involves schema changes:
- You MUST NOT assume migration strategy
- You MUST ask the user how to handle migrations
- You SHOULD consider: local-only, staging, production
- You SHOULD document migration steps

## Criteria Framework

Every task SHOULD have:

### Positive Criteria (What Success Looks Like)
- Specific, measurable outcomes
- User-facing behavior changes
- Technical requirements met
- Tests passing

### Negative Criteria (What to Avoid)
- Anti-patterns for this codebase
- Breaking changes to avoid
- Performance regressions
- Security concerns

## Completion Protocol

You MUST complete these steps:

```
> You MUST commit and push as a PR remotely
> You MUST remove worktree after PR is created
> You MUST run /issue-done when complete
```

This ensures:
- Work is saved and shared
- Clean workspace after completion
- Issue tracker stays in sync

## When to Use This Workflow

You SHOULD use this workflow for:
- Any feature implementation from an issue
- Bug fixes with tracked tickets
- Refactoring tasks
- Technical debt cleanup

## When NOT to Use This Workflow

You MAY skip this workflow for:
- Quick hotfixes (but you MUST still commit properly)
- Exploration without implementation intent
- Documentation-only changes
- Trivial typo fixes

## Integration with Other Skills

IFD works best with:
- `superpowers:write-plan` - For planning iterations
- `superpowers:code-review` - For plan validation
- `superpowers:brainstorm` - For design exploration
- `issue-dev` - For issue status management
- `ralph-loop` - For complex iterative work
- `frontend-design` - For UI implementation
