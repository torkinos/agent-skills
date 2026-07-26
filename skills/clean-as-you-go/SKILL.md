---
name: clean-as-you-go
description: >-
  Drives removal of legacy and dead code as part of every change: when new
  work supersedes old code, the old path with its tests, flags, and references
  is removed in the same change or recorded as a concrete follow-up. Use this
  skill when implementing a change that replaces existing behavior, doing a
  cleanup pass during or after a task, removing dead or unused code, or
  planning a migration off an old implementation or dependency. Do not use for
  style-only reformatting sweeps, whole-codebase tech-debt audits, or removing
  published APIs or external contracts without an explicit user decision.
license: MIT
metadata:
  short-description: Remove superseded and dead code with every change
---

# Clean As You Go

## Critical rules

- Replacing behavior includes removing what it replaces: the old implementation, its tests, fixtures, feature flags, config, exports, and doc references belong in the same change. Leaving both paths behind is an incomplete change, not a smaller one.
- If removal is genuinely out of scope or too risky now, record a concrete follow-up: what to remove, where it lives, and the condition that unblocks removal. Never leave superseded code silently.
- Evidence before deletion: confirm zero references with a repo-wide search (code, tests, scripts, CI, docs) and check for dynamic lookups (string-built identifiers, reflection, config-driven dispatch) before removing anything.
- Preserve with evidence, delete without: public APIs, external contracts, compatibility shims with a named consumer, trust-boundary validation, and still-needed operational code stay. Removing any of these requires an explicit user decision.
- Keep cleanup reviewable: unrelated cleanup goes in its own commit, and never reformat code that is not otherwise being touched.

## Workflow

1. **Plan removals with the change.** Before implementing, list what the change supersedes: code paths, dependencies, flags, config, docs. Put removal steps in the plan; anything deferred gets a follow-up entry with a removal condition.
2. **Sweep every touched file.** While editing, remove obvious dead weight in files being touched: unused imports and exports, commented-out code, unreachable branches, stale TODOs for finished work. Verify "unused" with a repo-wide search first.
3. **Remove the superseded path once the new one is verified.** After tests pass on the new behavior, delete the old implementation and everything that exists only to serve it: tests, fixtures, helpers, flags, config keys, dependency entries. Read `references/legacy-signals.md` for what to check per surface.
4. **Verify nothing dangles.** Search the repo for every removed symbol, file name, flag, and config key; expect zero live references. Re-run the project's build and full test suite after the removal pass, not only before it.
5. **Report the cleanup.** State what was removed, what was deferred with its follow-up entry, and anything protected that needs a user decision.

## Resources

- [`references/legacy-signals.md`](references/legacy-signals.md) — per-surface signals of removable legacy plus protected exceptions, and the follow-up entry shape. Read in step 3 before the removal pass.

## Validation

- A repo-wide search for each removed identifier returns no live references (changelogs and other historical records excepted).
- The project's build and full test suite pass after the removal pass.
- The final diff contains no commented-out code, no `_old`/`legacy`/`V2` duplicates of the new path, and no orphaned flags or config keys.

## Constraints

- Database migrations, audit trails, and versioned API surfaces are history, not legacy; never delete them as cleanup.
- Do not expand a cleanup pass into a redesign; route architecture-level findings into a plan or review instead of acting on them inline.
