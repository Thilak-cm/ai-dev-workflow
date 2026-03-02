# Audit Report Contract

This document defines the structured format that the **audit subagent** must output and the **fix subagent** consumes. It is the interface contract between the two agents — precision here determines fix quality.

## Report Format

```markdown
# Audit Report

## Metadata
- **Issue:** PEP-{id} — {title}
- **Branch:** {branch-name}
- **Diff scope:** {N} files changed, {+additions} / {-deletions}
- **Audit verdict:** CLEAN | HAS_FINDINGS
- **Blocker count:** {N}
- **Warning count:** {N}
- **Nit count:** {N}
- **User decision count:** {N}

## Scope Alignment

### Covered
- [AC-1] "{criterion text}" — addressed in `{file}:{line-range}`

### Missing (Under-delivery)
- [AC-{N}] "{criterion text}" — not found in diff.

### Extra (Scope Creep)
- `{file}:{line-range}` — {description}. Not tied to any acceptance criterion.

## Findings

### Blockers
{Issues that MUST be fixed before shipping.}

### Warnings
{Issues that SHOULD be fixed.}

### Nits
{Style, naming, minor improvements. Fix agent IGNORES these.}

### Needs User Decision
{Ambiguous issues the fix agent cannot resolve autonomously.}

## Summary
{1-3 sentence summary.}
```

## Finding Format

```markdown
#### {SHORT_TITLE}
- **File:** `{file_path}:{start_line}-{end_line}`
- **Category:** correctness | security | error-handling | dead-code | pattern-violation | test-gap | scope
- **What's wrong:** {1-2 sentence description}
- **Why it matters:** {1 sentence on impact}
- **Suggested fix:** {Concrete, actionable instruction}
- **Reference pattern:** `{file_path}:{line}` — {description of correct pattern}
```

## Severity Classification

**Blocker:** correctness bugs in core functionality, any security issue, missing acceptance criteria, zero test coverage for an AC, silent data loss.

**Warning:** edge case bugs, UX-degrading error handling, pattern violations, debug artifacts, edge case test gaps.

**Nit:** naming, verbosity, style preferences.

**Needs User Decision:** intentional scope creep, architectural tradeoffs, incomplete issue descriptions, performance tradeoffs.

## Verdict Rules

- **CLEAN** — zero blockers AND zero warnings.
- **HAS_FINDINGS** — one or more blockers OR warnings exist.
