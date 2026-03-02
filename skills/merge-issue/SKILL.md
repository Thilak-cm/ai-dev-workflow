---
name: merge-issue
description: "Merge a reviewed PR into dev via gh pr merge, clean up local and remote branches, move Linear issue to Done, and prompt for codebase overview refresh."
---

# Merge Issue

## Goal

Land a reviewed PR into `dev`, clean up branches, close out the Linear issue, and keep the codebase overview fresh. This is the final step after `/review-issue` opens a PR and CI passes.

## Workflow

### Phase 1: Pre-Merge Checks (Required)

1. Identify the PR to merge (from current branch's open PR)
2. **Check CI status** — Block if any checks failing or pending
3. Check for unresolved review comments — Block if any exist
4. Report full status to user before proceeding

### Phase 2: Merge PR (Approval Gate)

1. Verify PR targets `dev`
2. Merge via `gh pr merge` (repo's default strategy)
3. Confirm merge succeeded

### Phase 3: Local Cleanup (Required)

1. Switch to dev
2. Pull merged changes
3. Delete local feature branch (safe delete only)
4. Confirm clean state

### Phase 4: Linear Sync + Move to Done (Required)

1. Resolve the Linear issue from context/branch/PR
2. Comment with merge confirmation, commit range, PR URL, version
3. Move issue state to `Done`

### Phase 5: Codebase Overview Refresh (Prompt)

Ask user if they want to refresh the overview via `/codebase-context-scan`.

## Human Approval Gates

1. Before merging the PR — always confirm
2. Before deleting local branch if there are uncommitted stashes

## Guardrails

- **Do not merge if CI checks are failing or pending**
- Do not merge if PR has unresolved review comments
- Do not delete local branch until merge + pull are confirmed
- Do not update the wrong Linear issue
- Do not move to `Done` if merge actually failed
- Do not force-delete branches (`-D`) — use safe delete (`-d`) only
