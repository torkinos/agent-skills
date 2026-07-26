---
name: use-subagents
description: >-
  Delegates work to subagents safely on any coding-agent harness: splits work
  into bounded lanes, writes explicit assignment contracts, isolates readers
  from writers, verifies handoffs against the real diff, and cleans up every
  lane it created. Use this skill when delegating or decomposing work across
  subagents, running parallel research, review, or implementation lanes,
  requesting independent second-agent verification, or supervising and cleaning
  up agents that are already running. Do not use for plain background shell
  commands, servers, or build processes, for a single lookup you can do
  directly, or for deciding what the work itself should be.
license: MIT
compatibility: >-
  Policy only. Launching requires a subagent capability from the host: native
  subagent tools, an extension or plugin, or a controlled non-interactive agent
  CLI.
metadata:
  short-description: Split, assign, isolate, verify, and clean up subagent lanes
---

# Use Subagents

Delegation mechanics only. The calling task decides what work to do; this skill decides how that work is split, assigned, isolated, verified, and cleaned up.

## Critical rules

- Delegate by default whenever a safe launcher exists. "Small enough to do inline" is not a reason to skip delegation; when you do skip it, record why.
- The parent keeps decomposition, synthesis, dispositions and acceptance, plan and tracker ownership, user communication, every VCS operation, and cleanup. Each child gets one bounded job.
- Children never delegate further, never mutate version control (no commits, branches, worktrees, or merges, including through the shell), and never edit parent-owned plans, trackers, or integration state.
- One accountable launcher per lane, and preferably one launcher for the whole task. Two mechanisms driving the same lane is a defect.
- A handoff is evidence, not proof. Verify its claims against the actual diff and re-run the relevant checks in the parent before accepting anything.
- Never fire and forget. Every launched lane reaches a terminal state and is accounted for in the final report.
- Send only task-relevant context. Never send secrets, tokens, `.env` contents, or private transcripts.

## Workflow

1. **Split before launching.** Give each lane one independently verifiable outcome. Classify it as reader, writer, or independent validator, and record its scope, dependencies, expected output, and join point. Parallelize only independent lanes; coupled work becomes a single awaited lane. Cap fan-out at what the parent can actually integrate, verify, and clean up.
2. **Pick a launcher.** Take the first option that clears the safety bar below, and use it for every lane you can.
3. **Write the assignment.** Build each one from [`assets/assignment-template.md`](assets/assignment-template.md) using the matching profile in [`references/lane-roles.md`](references/lane-roles.md). Fill every field: an unstated boundary is an unenforced one.
4. **Isolate.** Apply the isolation table below. Before launching, record the baseline revision, the working directory, and any pre-existing dirty or untracked state, so the lane diff is attributable later.
5. **Supervise.** Confirm each lane actually started, then track it to completed, failed, cancelled, or timed out. Use a fresh child for new or independent judgment; reuse one only for follow-up on the same assignment. On a repeated identical failure, change the approach rather than retrying blind.
6. **Verify and integrate.** Read the handoff, then inspect the full lane diff against the baseline including untracked files. Re-run the relevant checks yourself. Integrate only what you verified, through the repository's normal method, and keep the lane alive until the join and acceptance checks pass.
7. **Clean up.** Follow the cleanup sequence below after every lane and again at task end.
8. **Report.** State the lanes used, their material results, what the parent verified, anything unresolved or failed, and every worktree, branch, workspace, or process created, integrated, retained, or removed.

## Launcher safety

A launcher is safe only when it provides all of:

- an exact working directory and context for the child
- an enforceable permission and write boundary
- observable start and completion
- captured output
- a timeout or equivalent bound
- cancellation for anything asynchronous
- known cleanup ownership

Take the first safe fit:

1. The host's native subagent capability (in Claude Code, the `Agent` tool, with `isolation: "worktree"` for concurrent writers).
2. A host extension or plugin.
3. A harness-specific adapter skill from this catalog.
4. A non-interactive agent CLI that meets every requirement above.
5. Nothing safe available: run the work in the parent. Block only when independence itself is the requirement, such as an unbiased review of the parent's own output.

## Isolation

| Lane type | Requirement |
| --- | --- |
| Reader | Read-only. A shared checkout is acceptable only when no concurrent writer can change what it sees; otherwise snapshot or isolate. |
| Writer | Sequential in one checkout, each lane stopped and dispositioned before the next starts. Concurrent writers only in separate worktrees or workspaces with non-overlapping file ownership. |
| Independent validator | Read-only, fresh context, and no access to the parent's reasoning beyond the artifact under review. |

Never mutate an active writer's working directory from the parent or a sibling lane.

## Cleanup

Run after each lane and again at task end, including when recovering from an interruption:

1. Stop or cancel any live children the launcher can still reach.
2. Integrate, retain, or explicitly mark disposable every piece of workflow-owned work.
3. Remove only resources that are safe to remove: created by this workflow, terminal, fully handled, not dirty or conflicted, not user-owned, and with no pending join.
4. Clear launcher runtime state only after review is finished, so evidence survives until it is no longer needed.
5. Retain and report anything unsafe or unknown, with the reason.

Leave no dangling worktrees, branches, processes, or sockets.

## Resources

- [`assets/assignment-template.md`](assets/assignment-template.md) — the fill-in assignment contract. Use in step 3 for every lane, and fill every field before launching.
- [`references/lane-roles.md`](references/lane-roles.md) — scout, research, worker, and validator profiles with each role's approach and required handoff. Read in step 3 to pick and adapt the role for each lane.

## Validation

- Every launched lane is in a terminal state and named in the final report.
- Every worktree, branch, workspace, or process the workflow created is integrated, retained with a stated reason, or removed.
- Every accepted change was checked against the real diff with the relevant checks re-run in the parent, never accepted on a child's claim alone.
- No secrets, credentials, or out-of-scope context appear in any assignment.

## Constraints

- This skill covers delegation mechanics only. It never sets task goals, acceptance criteria, or priorities; the calling task owns those.
- Do not delegate the parent's own judgment: framing, synthesis, final dispositions, and user communication stay in the parent.
- Do not use a subagent to work around a permission boundary the user placed on the parent.
