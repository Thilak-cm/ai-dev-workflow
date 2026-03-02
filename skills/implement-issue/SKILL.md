---
name: implement-issue
description: Start implementation of a Linear issue with automatic context loading, implementation-path tradeoff discussion, technical planning iteration, test-driven development, and Linear sync.
---

# Implement Issue

Bridge the gap between Linear issue creation and implementation. This skill automates the workflow of selecting a Linear issue, loading relevant codebase context, generating a technical execution plan with test specifications, discussing tradeoffs between viable implementation paths with the user, iterating until one path is finalized, discovering existing tests, implementing via TDD, and syncing progress back to Linear.

## Workflow Overview

The skill follows an 8-phase workflow:

1. **Issue Selection** - Interactively filter and select a Linear issue
2. **Context Loading** - Auto-load codebase overview, staleness check, optional explore
3. **Plan Generation** - Generate technical execution plan with file paths, test specs, and implementation path options
4. **Test Discovery** - Auto-detect related tests and establish baseline
5. **Plan Approval** - User reviews tradeoffs, iterates on plan, and approves a final implementation path
6. **Implementation** - Execute plan using TDD (write tests first, then code)
7. **Linear Sync** - Update Linear issue with branch, commits, and test results
8. **Manual Verification** - User manually verifies the e2e flow before moving to review

## Phase 1: Issue Selection

Start by selecting which Linear issue to work on. Use interactive filtering to narrow down options.

1. Ask user for optional filters (Assignee, State, Team, Labels, Priority)
2. Call `list_issues` with user-specified filters
3. Present top 20 results
4. User selects issue by ID or number
5. Call `get_issue` with selected issue ID, `includeRelations=true`

## Phase 2: Context Loading

Auto-load high-level overview, check for staleness, and spawn Explore subagent when deeper context is needed.

1. Read codebase overview, extract Area Map
2. Check overview staleness (5+ commits or 7+ days → warn user)
3. Infer relevant area tags from issue labels/keywords
4. If overview insufficient, spawn **codebase-explorer agent** for deeper context
5. Extract requirements from issue (acceptance criteria, user story, bug details)

## Phase 3: Plan Generation

Create a technical execution plan with specific file paths, test specifications, and explicit tradeoff analysis.

1. Map each acceptance criterion to code changes
2. Identify 2-3 viable implementation paths when reasonable
3. Compare tradeoffs for each path (speed, risk, complexity, maintainability)
4. Generate execution plan with:
   - Implementation Path Options (with pros/cons/risk profile)
   - Files to Modify/Create
   - Test Specification per acceptance criterion
   - Implementation Approach (TDD style)
   - Verification Checklist

**CRITICAL:** Every acceptance criterion MUST map to at least one test.

## Phase 4: Test Discovery & Baseline

1. Auto-detect related test files
2. Run baseline tests, capture results
3. Identify test gaps against acceptance criteria
4. Update plan with test discovery results

## Phase 5: Plan Approval

Get explicit user approval before making any code changes.

1. Present complete execution plan
2. Discuss implementation path tradeoffs with user
3. User confirms path (approve/edit/switch option)
4. Iterate until one path is finalized

**GUARDRAIL:** Do not modify any files until plan is approved and a feature branch is created.

## Phase 6: Implementation (TDD Approach)

1. Create a new git feature branch (mandatory before any edits)
2. **Write tests FIRST** (Red phase) for each acceptance criterion
3. **Implement code** to pass tests (Green phase)
4. **Verify test coverage** — block if any criterion lacks tests
5. **Refactor if needed** (Refactor phase)
6. Create commits referencing the issue ID

**TDD Cycle:**
```
For each acceptance criterion:
  1. RED: Write failing test (captures requirement)
  2. GREEN: Implement code to pass test (minimal implementation)
  3. REFACTOR: Clean up code while keeping tests green
```

## Phase 7: Linear Sync

1. Gather implementation details (branch, commits, files, test results)
2. Create detailed Linear comment with full progress
3. Do NOT change issue state — `/review-issue` handles that

## Phase 8: Manual Verification Gate

1. Present tailored verification checklist (UI, data, roles, API, bug fixes)
2. User must confirm "Yes, verified and working" before proceeding
3. If issues found: address them, re-run tests, ask again

**GUARDRAIL:** Do NOT suggest `/clear` + `/review-issue` until manual verification is complete.

## Edge Cases & Guardrails

- **No labels:** Use keyword matching to infer area tags
- **Plan too broad:** Suggest splitting into multiple issues
- **Multiple viable paths:** Present tradeoffs, let user decide
- **Issue already In Progress:** Warn user, ask if they want to proceed
- **No existing tests:** Create new test files
- **Baseline tests failing:** Warn and ask how to proceed
- **Test Coverage Blocking:** Hard stop if any criterion lacks tests
- **Feature Branch First:** ALWAYS create branch before any edits
- **No Pre-Plan Changes:** No file edits until plan is approved
- **No Unilateral Path Selection:** Discuss tradeoffs with user first

## Next Step

> After manual verification in Phase 8:
> 1. Run `/clear` to wipe implementation context
> 2. Run `/review-issue` — auto-detects issue from branch, audits with fresh eyes
> This ensures independent code review with no implementation bias.
