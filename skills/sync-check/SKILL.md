---
name: sync-check
description: Diagnostic "Context Auditor" that scans code changes for terminology drift against `CONTEXT.md` and architectural contradictions against `docs/adr/`. Use before `pr-review`, after significant refactors, or when names feel "off." Prevents term collisions (e.g., "Account" vs "Customer") and silent deviations from established architectural decisions. Pairs with `grill-plan` (to resolve contradictions found) and `pr-review` (downstream gate).
complexity: low
expected_duration: 10 minutes
---

# Sync Check (Context Auditor)

The discipline of ensuring that the code's implementation matches the project's **Domain Language** and **Architectural History**.

## Why this skill exists

As a codebase grows, it is easy for synonyms ("User", "Account", "Profile") to bleed into the code, diluting the domain model. Similarly, architectural decisions recorded in ADRs are often forgotten by developers (or LLMs) focused on local implementation. `sync-check` is the background auditor that surfaces these drifts before they calcify.

## When to use

- Before starting a `pr-review` (as a primary step).
- After a large refactor round in `tdd-rounds`.
- When you encounter a term in the code that isn't in `CONTEXT.md`.
- When the user asks "is this consistent with our model?" or "are we following our ADRs?"

## When to skip

- Initial project setup (pre-vocabulary).
- Pure doc/comment changes.
- Trivial typo/config fixes.

## Process

### 1. Identify the Scope

Map the files changed in the current branch or round to their corresponding domain contexts.
- Use `CONTEXT-MAP.md` if it exists.
- Otherwise, use the root `CONTEXT.md`.

### 2. Terminology Audit

Scan the `git diff` for new or changed public identifiers (classes, functions, types, variables).

- **Term Collision**: Check if a new term is listed as an `_Avoid_:` alias in `CONTEXT.md`.
- **Fuzzy Mapping**: Check if a term exists in the code but is missing from the glossary.
- **Synonym Drift**: Check if the code uses two different words for the same domain concept.

### 3. ADR Consistency Check

Read the `docs/adr/` entries relevant to the changed packages.

- **Direct Contradiction**: Does the change perform an action explicitly forbidden by an ADR (e.g., using an ORM when an ADR mandates manual SQL)?
- **Surprise Deviation**: Does the change introduce a pattern that warrants a new ADR (hard to reverse, surprising, real trade-off)?

### 4. Report Drifts

Output a numbered list of findings. For each:
- **Location**: `file:line` or `ADR-NNNN`.
- **Severity**: `Blocker` (direct contradiction/collision) or `Suggestion` (fuzzy mapping).
- **Finding**: "Code uses 'Account', but `CONTEXT.md` specifies 'Customer'."
- **Recommended Action**: "Rename to 'Customer' or update `CONTEXT.md` if the concept is genuinely new."

## Done when

- All changed files have been cross-referenced with the glossary and relevant ADRs.
- Every "Avoid" alias found in the diff has been flagged.
- Every architectural deviation from active ADRs has been surfaced.

## Handoff

- If contradictions are found → run `grill-plan` to resolve the terminology or architectural mismatch.
- If clean → proceed to `pr-review`.
