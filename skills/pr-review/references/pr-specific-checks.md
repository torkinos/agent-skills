# PR-specific checks

These cover what is unique to reviewing a pull request someone else authored: you did not see the reasoning, the description may not match the diff, and other signals already exist on the PR. The right-hand column is as binding as the left. Those items are not findings, and raising them as "minor notes" is exactly the noise a good reviewer is trusted to leave out.

| Check | What to look for | Not a finding |
|---|---|---|
| Stated intent | The diff delivers what the title, description, and linked issue promise, and the acceptance criteria are met rather than approximated. | A different but equally valid approach to the same outcome. |
| Undisclosed changes | Behavior changed outside what the description covers: silent config edits, unrelated refactors, a bug fix smuggled into a feature PR, a default quietly flipped. | Mechanical churn the change forces, such as an import reorder or a signature updated at every call site. |
| Blast radius | Consumers outside this repository: published APIs, event payloads, shared types, database columns, and anything another service or client reads. | Internal signatures where every caller moves in the same PR. |
| Rollout and revert | Migrations that are safe to run before the code ships, backfills that can resume, feature flags with a defined off state, and whether reverting the PR leaves the system consistent. | Rollout ceremony for a change with no deployed state to break. |
| Dependencies | New dependencies and version bumps: what the addition is for, whether the repo already solves it, major-version jumps buried in a lockfile, and packages with install-time scripts. | Routine patch bumps from an automated dependency bot. |
| Secrets and config | Credentials, tokens, internal hostnames, or customer data in code, fixtures, tests, logs, or error messages. Any of these is `Critical` regardless of how small the diff is. | Placeholder or obviously fake values in test fixtures. |
| CI signal | Read the check results before writing anything. Point at a failing check rather than restating what it already says, and treat green CI as evidence only for what it actually tests. | Re-reporting a lint or type error CI has already surfaced on the PR. |
| Existing conversation | Read the review comments already on the PR. If another reviewer made your point, reference and reinforce it instead of adding a duplicate thread. | Repeating an open thread the author has not yet had a chance to answer. |
| Fork-authored automation | In a PR from a fork, changes to CI workflows, build scripts, install hooks, or anything that executes in the pipeline. Treat these as a trust boundary. | Workflow edits in a branch PR from a maintainer with existing write access. |
| Test evidence | New behavior is covered, the tests fail when the behavior breaks, and no test was weakened, skipped, or asserted into passing. | Missing tests for behavior this PR did not touch, or a demand for a coverage number. |
| Reviewability | Only when it genuinely obstructs review: unrelated changes bundled into one PR, or a diff so large the author should split it. `Optional` tier, and only once. | Commit-message style, branch naming, or squash preference. |

## Comment craft

The comment text is pasted as written, so write it as the user would send it.

- Address the author. One issue per comment, anchored to the line where it is clearest.
- State what breaks and under which input, then the smallest safe fix in one line. Do not write the fix for them.
- At `Supported` confidence, ask rather than assert: "Is `items` guaranteed non-empty here?" costs nothing if the answer is yes.
- No condescension, no lecturing, no praise padding. A note that an area was checked and is sound belongs in the report only when it tells the user something.
- Superseded code, dead branches, and debug output left behind in the diff are a legitimate finding under the `clean-as-you-go` contract. Pre-existing legacy the PR did not touch is not.
