---
name: requesting-code-review
description: Request code review against requirements, modern Angular v22+ best practices, and the repository style guide. Use when completing tasks, implementing major features, or before merging.
---

# Requesting Code Review

Dispatch a code reviewer subagent to catch issues before they cascade. The reviewer gets precisely crafted context for evaluation – never your session's history. This keeps the reviewer focused on the work product, not your thought process, and preserves your own context for continued work.

**Core principle:** Review early, review often.

## When to Request Review

**Mandatory:**

- After each task in subagent-driven development
- After completing a major feature
- Before merge to main

**Optional but valuable:**

- When stuck (fresh perspective)
- Before refactoring (baseline check)
- After fixing complex bug

## How to Request

**1. Choose a review range:**

Inspect the working tree and pick the base deliberately:

```bash
git status --short
BASE_SHA=$(git merge-base HEAD origin/main) # branch review
# or BASE_SHA=<known-plan-checkpoint>
HEAD_SHA=$(git rev-parse HEAD)
```

If the work is uncommitted, either commit/checkpoint it first or state that the review must include
the working-tree diff. Do not default to `HEAD~1` unless this task is exactly one commit.

_Done when_ `BASE_SHA`, `HEAD_SHA`, and any working-tree diff scope are explicit.

**2. Gather review context:**

Write a compact handoff for the reviewer:

- `{DESCRIPTION}` – Brief summary of what you built
- `{PLAN_OR_REQUIREMENTS}` – What it should do
- `{VERIFICATION}` – Commands run, results, and known failures or skipped checks
- `{BASE_SHA}` – Starting commit
- `{HEAD_SHA}` – Ending commit

_Done when_ the reviewer can understand the intended behavior, changed range, and verification
state without reading this session's history.

**3. Dispatch code reviewer subagent:**

Dispatch a `general-purpose` subagent, filling the template at
[references/code-reviewer.md](references/code-reviewer.md)

If subagents are unavailable, run the same template yourself as a read-only review and say that no
independent subagent was available.

_Done when_ the review is returned, or the fallback review limitation is reported.

**4. Verify and act on feedback:**

- Check every reviewer finding against the codebase before editing
- Fix valid Critical issues immediately
- Fix valid Important issues before proceeding
- Note Minor issues for later
- Push back on incorrect findings with file/line evidence or test output

_Done when_ each Critical and Important finding is fixed, explicitly deferred by the user, or
rejected with evidence.

## Example

```text
[Just completed Task 2: Add verification function]

You: Let me request code review before proceeding.

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[Dispatch code reviewer subagent]
  DESCRIPTION: Added verifyIndex() and repairIndex() with 4 issue types
  PLAN_OR_REQUIREMENTS: Task 2 from docs/superpowers/plans/deployment-plan.md
  VERIFICATION: pnpm test -- verify-index passed; pnpm lint passed
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661

[Subagent returns]:
  Strengths: Clean architecture, real tests
  Issues:
    Important: Missing progress indicators
    Minor: Magic number (100) for reporting interval
  Assessment: Ready to proceed

You: [Fix progress indicators]
[Continue to Task 3]
```

## Integration with Workflows

**Subagent-Driven Development:**

- Review after EACH task
- Catch issues before they compound
- Fix before moving to next task

**Executing Plans:**

- Review after each task or at natural checkpoints
- Get feedback, apply, continue

**Ad-Hoc Development:**

- Review before merge
- Review when stuck

## Red Flags

**Never:**

- Skip review because "it's simple"
- Ignore Critical issues
- Proceed with unfixed Important issues
- Argue with valid technical feedback

**If reviewer wrong:**

- Push back with technical reasoning
- Show code/tests that prove it works
- Request clarification

See template at: [references/code-reviewer.md](references/code-reviewer.md)
