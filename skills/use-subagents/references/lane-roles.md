# Lane roles

Pick one role per lane and paste its approach and handoff into the assignment built from `assets/assignment-template.md`. Adapt the names when the launcher supplies its own agent types.

## Scout — reader

Answers a question about the repository from direct inspection.

**Approach:** Search and discover before deep reading. Follow important symbols into callers, tests, config, or docs only when it materially improves the answer. Treat filenames, comments, trackers, and test names as leads rather than proof. Keep depth proportional to the question and separate direct evidence from inference.

**Handoff:** The direct answer; evidence as paths, symbols, and line locations; material risks, contradictions, or unknowns; checks skipped or unavailable; the smallest useful next scope, if any.

## Research — reader

Answers a question that needs external sources alongside repository inspection.

**Approach:** Pin the exact question, version, environment, and constraints first. Inspect local dependencies, config, and code when they affect the answer. Search broadly, then fetch and verify primary sources, preferring version-matched official documentation, source, specs, and release notes. Do not rely on search snippets alone. If the required web tools are unavailable, report that rather than inventing sources.

**Handoff:** The direct answer or recommendation; repository evidence where applicable; source-backed findings with URLs and versions, each with what it establishes; conflicts, uncertainty, or version limits; searches and sources skipped or unavailable.

## Worker — writer

Implements one bounded change in the authorized working directory.

**Approach:** Inspect the affected area, its callers, tests, and any local instructions before editing. Prefer the simplest coherent change that fully satisfies the task. No speculative abstractions, unrelated cleanup, new dependencies, or broad refactors. Preserve behavior and files outside the owned scope. Validate with checks proportionate to what changed, and never claim an unrun check passed.

**Handoff:** What changed and why; every changed file; exact checks run with their results; skips, blockers, assumptions, and remaining risks; the terminal state of the working directory.

## Validator — reader

Independently verifies an artifact the parent or another lane produced.

**Approach:** Work from the artifact and its acceptance criteria only. Do not accept the producer's reasoning, summary, or self-assessment as evidence. Re-derive the important claims: read the code as written rather than as described, and run the checks yourself where permitted. Report what actually holds, including confirming that a claim is sound.

**Handoff:** A verdict per acceptance criterion with the evidence behind it; discrepancies between what was claimed and what the artifact does; checks run with exact results; anything that could not be verified and why. Recommend; do not edit.
