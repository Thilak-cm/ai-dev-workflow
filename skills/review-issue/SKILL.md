---
name: review-issue
description: "Independent code review in a fresh session: audit diff against Linear issue, fix-loop until clean, version bump, commit, push, open PR against dev, and move Linear to In Review."
---

# Review Issue

## Goal

Provide an independent quality gate between implementation and production. This skill runs in a **fresh Claude session** (not the one that wrote the code) and orchestrates subagents to audit the diff, fix issues, and re-audit in a loop until the code is clean — then ships it.

The orchestrator stays thin. Heavy work (reading diffs, auditing code, making fixes) is delegated to subagents so the main context is protected and the audit loop can run as many times as needed without degradation.

## Architecture

```
Orchestrator (this skill — thin, stays in main context)
    |
    +-- Explore subagent (Task/Explore)              — only if diff is complex
    +-- Audit subagent (.claude/agents/code-auditor)  — produces structured review report
    +-- Fix subagent (.claude/agents/code-fixer)      — consumes report, makes fixes
```

The audit report contract defines the exact format the audit agent outputs and the fix agent consumes.

## Workflow

### Phase 1: Context Load (Orchestrator)

1. **Identify the Linear issue** (from branch name or user)
2. **Capture the diff** (committed + uncommitted changes vs dev)
3. **Load the high-level overview**
4. **Assess whether Explore agent is needed** (4+ files, shared infrastructure, new components, security rules)

### Phase 1b: Codebase Explorer Agent (Conditional)

Only if Phase 1 determined exploration is needed. Spawn codebase-explorer with exploration_focus="review".

### Phase 2: Audit Subagent

Core quality gate. Passes Linear issue + diff + overview + explore summary to the code-auditor agent. Receives structured audit report.

### Phase 3: Process Audit Results (Orchestrator)

1. Display full audit report to user
2. Handle "Needs User Decision" items first
3. If `CLEAN` → proceed to Phase 5 (Version Bump)
4. If `HAS_FINDINGS` → proceed to Phase 4 (Fix Loop)

### Phase 4: Fix Loop

```
max_iterations = 3

for i in 1..max_iterations:
    if verdict == CLEAN:
        break → proceed to Phase 5
    if i == max_iterations:
        STOP — surface remaining findings to user
    else:
        spawn fix agent → spawn audit agent → continue loop
```

Each re-audit spawns a **fresh** audit agent with no memory of previous audits.

### Phase 5: Version Bump (Orchestrator)

1. Infer bump type (patch/minor/major) from issue and diff
2. Ask user to confirm
3. Run version script, update changelog

### Phase 6: Commit + Push + PR (Orchestrator)

1. Stage and commit with clear messages
2. Push feature branch
3. Open PR via `gh pr create` targeting `dev` with audit summary in body

### Phase 7: Linear Sync (Orchestrator)

1. Comment on Linear issue with branch, PR, audit summary, test results
2. Move issue to `In Review`

## Human Approval Gates (Do Not Skip)

1. **Before fixing** — after showing audit report
2. **After 3 failed fix loops** — surface remaining findings
3. **Version bump type** — always confirm
4. **Changelog entry** — show for review
5. **Before pushing + opening PR** — confirm ready to ship

## Guardrails

- **Fresh session required:** Audit value comes from independence
- **Subagents do the heavy lifting:** Orchestrator does NOT read full diff itself
- **Each audit is fresh:** Re-audits spawn new audit agent with no memory
- **Max 3 fix iterations:** Escalate to user if unresolved
- **Do not merge:** Opens PR only. `/merge-issue` handles merging
- **Do not invent test results:** Report actual output
- **Do not push if tests fail:** Unless user explicitly accepts risk
