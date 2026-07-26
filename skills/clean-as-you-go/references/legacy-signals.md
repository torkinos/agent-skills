# Legacy signals

Candidate signals for the removal pass. Every deletion still needs the evidence rule from `SKILL.md`: a repo-wide zero-reference search plus a dynamic-lookup check. When something is only mostly dead, prefer in order: delete, use the new path directly, inline locally, fold into an existing abstraction.

| Surface | Removable signals | Protected — keep or ask |
|---|---|---|
| Source | Superseded implementations kept "just in case"; `_old`/`legacy`/`V2`-suffixed duplicates of the current path; commented-out blocks; unreachable branches; unused exports, parameters, and return fields; wrappers that only forward to the new path | Public or published API surface; polyfills and shims with a named consumer; trust-boundary validation |
| Tests | Tests and fixtures exercising only the removed path; skipped or disabled tests for behavior that no longer exists; snapshot files with no matching test | Regression tests that still guard the new path; contract or compatibility tests with an external consumer |
| Dependencies | Packages only the removed code imported; overlapping packages after consolidation; type packages for removed dependencies | Transitive and peer requirements; packages used by scripts, CI, or tooling outside the main source tree |
| Config and flags | Feature flags fully rolled out or guarding removed features; env vars nothing reads; config keys with no consumer; CI steps for removed targets | Operational kill-switches with an owner; deploy contracts other systems read; externally managed secrets |
| Docs and scripts | Setup steps for removed tooling; README sections describing removed behavior; one-off scripts for completed migrations | Changelogs and ADRs (they record history); runbooks for versions still deployed |
| Data and schema | Nothing removable by default on this surface | Past database migrations, audit logs, backups, and anything needed to reproduce or roll back a deployed state; schema changes get their own planned migration, never a cleanup deletion |

## Follow-up entry shape

When removal is deferred, record it where the project actually tracks work (plan file, issue tracker, team TODO doc) with all four parts:

- **What:** exact paths, symbols, flags, or package names to remove.
- **Why deferred:** the concrete risk or scope reason, not "later".
- **Removal condition:** the observable event that unblocks it (for example: flag at 100 percent for a full release cycle; last consumer migrated; contract expiry).
- **Owner or trigger:** who acts, or which recurring checkpoint re-evaluates it.
