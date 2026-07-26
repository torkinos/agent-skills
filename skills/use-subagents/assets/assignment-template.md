# Assignment template

One bounded job per lane. Fill every field before launching — an unstated boundary is an unenforced one. Send only task-relevant context: never secrets, tokens, `.env` contents, or private transcripts.

```text
Role: <scout | research | worker | validator, or the launcher's equivalent>
Mode: <reader | writer>
Objective: <one concrete, independently verifiable outcome>
Working directory: <exact authorized cwd or workspace>
Baseline: <revision or pre-lane state, including known dirty or untracked files>
Context: <files to read first, established facts, open questions>
Owned scope: <paths, behaviors, acceptance criteria>
Non-goals: <explicit exclusions and prohibited areas>
Dependencies and join point: <inputs from other lanes, and where this result feeds>
Permissions: <tools and paths allowed>
VCS: no commits, branches, worktrees, merges, or cleanup, including through the shell.
     Read-only version-control commands only if this line explicitly allows them.
Ownership: do not edit parent-owned plans, trackers, manifests, or integration state.
Delegation: never spawn, delegate to, or coordinate another agent.
Validation: <required checks>. Report exact results and justify every skip.
Stop when: <completion condition>. Timeout: <bound>.
Handoff: files read and files changed; findings or changes with reasons; decisions made;
         checks run with exact results; skips; risks; blockers; remaining work;
         pointers to evidence rather than transcripts.
```

State in the assignment that the parent owns full-diff review, integration, version control, and cleanup, so the child does not attempt them.
