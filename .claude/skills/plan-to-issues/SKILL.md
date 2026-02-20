---
name: plan-to-issues
description: Explore the codebase, draft a feature plan, get user confirmation, then automatically publish one GitHub issue per task plus a parent tracking issue. Use when the user invokes /plan-to-issues or asks to "plan and create GitHub issues".
argument-hint: <feature or task description>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Bash(git *), Bash(gh *), AskUserQuestion
---

You are orchestrating a two-phase workflow: **Plan** then **Publish**.
Do NOT use `EnterPlanMode` or `ExitPlanMode`. Do NOT write or edit any code files.

---

## Phase 1: Plan

### Step 0 — Get a description
If `$ARGUMENTS` is empty, use `AskUserQuestion` to ask: "What feature or task would you like to plan?"

### Step 1 — Explore the codebase
Use `Read`, `Glob`, `Grep`, and `Bash(git log --oneline -20)` to understand the project structure and any context relevant to `$ARGUMENTS`. Look at existing files, conventions, and related prior work.

### Step 2 — Draft the plan
Write out the plan as a structured markdown message to the user using this exact template (heading levels matter for Phase 2 parsing):

```
# Plan: <title>

## Context
<2–4 sentences describing the problem and intent>

## Tasks

### Task 1: <imperative title>
**Goal:** <one sentence describing the outcome>
**Details:**
- <specific file to create or modify, or action to take>
- <additional detail>
**Acceptance criteria:**
- <observable, verifiable outcome>

---

### Task 2: <imperative title>
**Goal:** <one sentence>
**Details:**
- <detail>
**Acceptance criteria:**
- <observable outcome>

---

(repeat for all tasks)

## Verification
<How to confirm the entire plan is complete end-to-end>
```

Every task MUST use the `### Task N: <title>` heading — this is the parsing anchor used in Phase 2.

### Step 3 — Ask for approval
After presenting the plan, use `AskUserQuestion` with a single question:

- Question: "Does this plan look good? Should I create GitHub issues for these tasks?"
- Options:
  - "Yes, create the issues" → proceed to Phase 2
  - "No, let me refine first" → ask the user what to change, update the plan, and re-ask

Do NOT proceed to Phase 2 until the user explicitly confirms.

---

## Phase 2: Publish issues

Execute the following steps only after the user confirms.

### Step 1 — Ensure the `from-plan` label exists
Run:
```
gh label create "from-plan" --color "0075ca" --description "Issue generated from a Claude Code plan" --force
```
The `--force` flag makes this idempotent. If this fails due to permissions, warn the user and continue without the label.

If `gh` is not authenticated, run `gh auth status` to diagnose and instruct the user to run `gh auth login` before retrying.

### Step 2 — Create one issue per task
For each `### Task N: <title>` section in the plan you generated:

1. Extract:
   - **Title**: the text after `### Task N: `
   - **Goal**: the text after `**Goal:**`
   - **Details**: the bullet list under `**Details:**`
   - **Acceptance criteria**: the bullet list under `**Acceptance criteria:**`

2. Create the issue:
```
gh issue create \
  --title "<task title>" \
  --label "from-plan" \
  --body "<formatted body>"
```

Format the body as:
```markdown
## Goal
<goal sentence>

## Details
<details bullets>

## Acceptance criteria
<acceptance criteria bullets>

---
*Generated from plan: <plan title>*
```

3. Capture the URL printed by `gh issue create` — you will need it for the tracking issue.

If `gh issue create` fails, display the body that was attempted, and offer the user the choice to retry or skip.

### Step 3 — Create the tracking issue
After all task issues are created, create a parent tracking issue:

- **Title**: `[Plan] <plan title>`
- **Label**: `from-plan`
- **Body**:
```markdown
## Context
<paste the ## Context section from the plan>

## Tasks
- [ ] <task 1 title> — <task 1 issue URL>
- [ ] <task 2 title> — <task 2 issue URL>
...

## Verification
<paste the ## Verification section from the plan>
```

### Step 4 — Print summary
Output a summary like:

```
Plan issues created successfully!

Tracking issue: <tracking issue URL>

Task issues:
  1. <task 1 title> — <url>
  2. <task 2 title> — <url>
  ...
```

---

## Error handling

| Situation | Action |
|-----------|--------|
| `gh` not authenticated | Run `gh auth status`, tell user to run `gh auth login` |
| `gh issue create` fails | Show the attempted body, offer to retry or skip |
| Label creation fails (permissions) | Warn the user, then continue without the label |
