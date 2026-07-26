---
name: fresh-eyes-review
description: >-
  Routes just-finished work through an independent reviewer agent and then
  triages what comes back: a fresh-context lane reviews the change against the
  task's intent and returns prioritized, evidence-backed findings, and the agent
  that did the work decides for each one whether to apply, verify, defer,
  escalate, or reject it with a stated reason. Use this skill when a substantive
  implementation is finished, when asked to double-check, self-review, or
  independently verify work before handing it off, or when deciding what to do
  with review findings that have already arrived. Do not use for reviewing code
  you did not just write, such as an inbound pull request, for trivial or
  mechanical edits like renames and version bumps, or for turns that produced no
  artifact to review.
license: MIT
compatibility: >-
  Requires a subagent capability for the independent lane. Without one, report
  that an independent review was not possible instead of substituting
  self-review.
metadata:
  short-description: Independent review of finished work, then reasoned triage
---

# Fresh Eyes Review

An independent agent reviews the finished work; the agent that did the work decides what happens to each finding. Neither half works alone. Review without triage causes churn, and triage without independence just re-confirms the assumptions that produced the work.

## Critical rules

- The reviewer runs in a fresh context and receives only the artifact, the task's goal, and the acceptance criteria. Never send it your reasoning, your confidence, or your view of what is correct: anchoring the reviewer defeats the purpose of running it at all.
- Use a reviewer of the same or stronger reasoning capability, never weaker. When the harness cannot select a model, use the default and say so in the report.
- The reviewer reports and never edits. Fixes belong to the agent that owns the work.
- Admit a finding only with a concrete failure, realistic reachability, and practical impact within the reviewed change. Style preferences, hypotheticals, and speculation are not findings and must not be smuggled in as caveats.
- Every finding gets exactly one disposition and a reason that would survive a challenge. Applying findings to be agreeable is as much a failure as rejecting the inconvenient ones.
- One follow-up round at most, and only when a fix plausibly introduced a new defect. Re-reviewing the whole change again is churn, not diligence.
- Without a subagent capability, an independent review is not possible. Say so plainly rather than presenting self-review as equivalent.

## Workflow

1. **Decide whether to review.** Run the loop for new behavior, multi-file changes, anything touching authentication, permissions, money, or persisted data, and anything the user flagged as important. Skip it for renames, version bumps, formatting, generated files, and doc typos, and say you skipped it. A reviewer lane roughly doubles the cost of the task, so scale it to the change.
2. **Launch the reviewer lane.** Follow the `use-subagents` policy, using its read-only validator role. Send the diff or changed files, the task's goal and acceptance criteria, and how to run the tests. Withhold your own reasoning and self-assessment.
3. **Point the reviewer at the dimensions.** Have it work through [`references/review-dimensions.md`](references/review-dimensions.md), which also records what does not count as a finding on each dimension.
4. **Take delivery of findings.** Each one carries a stable `R<n>` identifier, a priority, a confidence, a location, the concrete failure, and the smallest safe fix. "No material findings" is a valid and useful result.
5. **Triage every finding.** Assign one disposition from the table below, with a reason. Findings that arrive from outside this loop — a human reviewer, CI, or another tool — enter here and follow the same contract.
6. **Apply what you accepted.** Make the smallest fix that resolves the finding, update the affected tests, and re-run the relevant checks. Do not widen a fix into a refactor.
7. **Run at most one follow-up round.** Scope it to the fixes you applied and the boundaries they touched, preserving the original finding identifiers. If it surfaces no material delta, the loop is done.
8. **Report.** List every finding identifier with its priority, disposition, and one-line reason, plus anything deferred or escalated. Include rejections: a finding you dismissed is information the user may disagree with.

## Finding format

| Priority | Meaning |
| --- | --- |
| `P1` | Correctness, security, data-loss, or contract break in the new work. Blocks finishing. |
| `P2` | Real defect or missing verification that will cause rework. Fix now unless it needs a user decision. |
| `P3` | Genuine but non-blocking: a clearer approach, a missing test, an unhandled edge. Apply when cheap and in scope. |
| `P4` | Observation with no required action. Report it, act only if asked. |

Confidence is **Confirmed** when the reviewer read the code path or ran the check, and **Supported** when it reasoned from evidence without executing anything. Anything weaker than Supported is not yet a finding and stays out of the report.

## Dispositions

| Disposition | Use when | Then |
| --- | --- | --- |
| **Apply** | The failure is real, reachable, and in scope. | Fix it now, smallest change first. |
| **Verify first** | Plausible but unproven, or rests on an assumption about runtime behavior. | Reproduce or check it, then apply or reject on what you actually find. |
| **Defer** | Real but genuinely outside this change's scope. | Record a follow-up with what, why deferred, and the condition that unblocks it, the way `clean-as-you-go` records deferred removals. |
| **Ask user** | Material trade-off, scope change, or a protected surface such as a public API or external contract. | Present the finding with options and a recommendation; do not act unilaterally. |
| **Reject** | Factually wrong, unreachable, out of scope, or contradicting a deliberate decision. | State the reason. "It would require rework" is not one. |

## Validation

- Every finding identifier appears in the report with a disposition and a reason.
- Applied fixes are covered by tests or an explicit statement of why they are not, and the relevant checks were re-run after the last fix, not before it.
- The reviewer's independence held: it ran in a fresh context, received no self-assessment, and made no edits.
- The reviewer lane reached a terminal state and was cleaned up per the `use-subagents` policy.

## Constraints

- This is self-verification of work just produced. Reviewing code you did not write, as a deliverable for someone else, is a different job with a different output.
- Do not let the review become a redesign. Architecture-level findings are deferred or escalated, never acted on inline.
- Do not run the loop to manufacture confidence on work you already know is unverified. Run the tests first; a reviewer is not a substitute for checks you skipped.
