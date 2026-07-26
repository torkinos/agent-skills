# Review dimensions

Work through each dimension against the change under review. The right-hand column is as binding as the left: those items are not findings, and reporting them as caveats or "minor notes" is the noise this loop exists to prevent.

| Dimension | Check | Not a finding |
|---|---|---|
| Intent | The change does what the task asked, and the acceptance criteria are actually met rather than approximated. Requirements silently dropped or reinterpreted. | A different but equally valid approach to the same outcome. |
| Correctness | Boundary conditions, empty and single-element cases, off-by-one errors, null and undefined paths, type coercion, and early returns that skip required work. | Defects in code the change did not touch, unless the change made them reachable. |
| Failure paths | Errors are caught where they can be handled, failures surface rather than being swallowed, partial failure leaves no inconsistent state, and retries are safe to repeat. | Hardening for inputs that cannot occur at that layer. |
| Trust boundaries | Validation and authorization at the boundary rather than after trusted parsing, no injection through interpolated queries or commands, no secrets in code, logs, or error messages. | Duplicate checks on an already-validated internal path. |
| Data and contracts | Schema and migration safety, backward compatibility for existing callers and stored data, and changes to published APIs or event payloads that consumers depend on. | Internal signatures with all callers updated in the same change. |
| Concurrency and state | Shared mutable state, races between read and write, non-idempotent side effects, and cleanup that does not run on every exit path. | Theoretical interleavings with no realistic overlap. |
| Tests | New behavior is actually covered, tests fail when the behavior breaks, and assertions check outcomes rather than implementation detail. Tests weakened or skipped to make a change pass. | Missing tests for unchanged behavior, or a demand for coverage percentages. |
| Leftover work | Superseded code, dead branches, orphaned flags, commented-out blocks, and debug output left in the change, per the `clean-as-you-go` contract. | Pre-existing legacy unrelated to this change. |
| Fit | The change follows the conventions, idioms, and error-handling patterns already in the surrounding code. | Personal style preference where the codebase has no established convention. |

## Reporting

Report only what clears the evidence gate in `SKILL.md`: a concrete failure, realistic reachability, and practical impact. State the location, the failure, and the smallest safe fix; do not write the fix. Deduplicate findings that share one root cause into a single entry. Concluding "no material findings" is a valid outcome and preferable to padding the report.
