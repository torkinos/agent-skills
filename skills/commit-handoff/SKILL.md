---
name: commit-handoff
description: >-
  Holds every version-control and outward-facing action for the user's explicit
  approval and hands back what to run instead: never stages, commits, pushes,
  tags, publishes, deploys, or posts on a pull request or issue on its own, and
  returns a commit message matched to the repository's existing convention, a
  branch name, a pull request title and description, a scan of what would be
  exposed, and a proposed split when the change is too large to review in one
  piece. Use this skill when a unit of work is finished and ready to commit,
  push, or open as a pull request, when the user asks for a commit message,
  branch name, or pull request title, or before any action that would publish,
  share, or expose the user's work. Do not use for judging whether the work
  itself is correct, which belongs to fresh-eyes-review, for reviewing a pull
  request someone else opened, which belongs to pr-review, or for read-only
  version control such as inspecting history, diffs, or branches.
license: MIT
metadata:
  short-description: Gated commits and pushes, with the handoff written for you
---

# Commit Handoff

Nothing enters version control and nothing leaves this machine without the user saying so. What finished work produces is a handoff: the exact commands, ready to run, for the user to run.

## Critical rules

- **Gated actions, named.** Never run `git add`, `commit`, `push`, `tag`, `merge`, `rebase`, `cherry-pick`, `revert`, `commit --amend`, `reset --hard`, `checkout -f`, `branch -D`, or `stash drop`; never run `gh pr create|review|comment|merge|close`, `gh issue create|comment|close`, `gh release create`, or `gh gist create`; never publish to a registry or run a deploy command. Each needs an explicit instruction for that action.
- **Reading is never gated.** `git status`, `diff`, `log`, `show`, `blame`, `branch --list`, `remote -v`, and read-only `gh` need no permission and are not worth announcing.
- **One grant, one action, one scope.** "Commit this" authorizes one commit, not a push. "Push" authorizes this branch to its existing remote, not a force-push and not a new one. Permission never carries to the next unit of work.
- **Publishing is irreversible.** Anything pushed, posted on a pull request or issue, or published to a registry must be treated as permanently public, deletion included. When in doubt, hand back the command instead of running it.
- **Never rewrite the record.** No amend, rebase, force-push, or history edit the user did not ask for. No attribution, co-author trailers, or tool advertising in a suggested message unless the repository's own history already carries them.
- **Match the repository, not a preference.** Read `git log` before proposing a format, and say which convention was matched. Conventional Commits only when the repo already uses it or shows no consistent style.
- **Scan before suggesting.** Never propose a commit for changes not yet inspected for secrets, `.env` files, customer data, oversized binaries, and debug leftovers.
- **Say what was not done.** Every handoff ends by stating that nothing was staged, committed, pushed, or posted. The user should never have to check.

## Workflow

1. **Take stock.** Read `git status --short`, `git diff`, `git diff --staged`, `git diff --stat`, `git log --oneline -15`, the current branch, and the remote. Never stage anything in order to see what would be committed.
2. **Check what would be exposed.** Work [`references/pre-publish-checks.md`](references/pre-publish-checks.md). Report anything found *before* proposing a message; a blocking hit ends the handoff there until the user decides.
3. **Check the branch.** If the current branch is the default (`main`, `master`) or unrelated to this work, say so and propose a branch name before anything else. It is the cheapest mistake to prevent and the most annoying to undo.
4. **Read the repository's convention.** From `git log`, take the subject style, prefix scheme, capitalization, length, scope naming, trailers, and issue-reference format.
5. **Size the change.** Apply the split table below. A warranted split comes before the commit message, since it changes what the messages are.
6. **Write the handoff.** Fill [`assets/handoff-output.md`](assets/handoff-output.md).
7. **Hand over the commands.** Give the exact commands, in order, as copy-paste text with explicit paths. Do not run them.
8. **Stop.** If the user then authorizes an action, do exactly that action and nothing adjacent, then restate what is still ungated.

## Commit message

The repository's existing style wins. Use this shape only when the repo already uses Conventional Commits or shows no consistent convention:

```text
<type>(<optional scope>): <imperative subject, 72 characters or fewer, no trailing period>

<why the change was needed and what it does; omit when the subject already says everything>

BREAKING CHANGE: <what breaks and what to do instead>
Refs: #<issue>
```

Types: `feat`, `fix`, `refactor`, `perf`, `docs`, `test`, `build`, `ci`, `chore`, `revert`. One type per commit — when two apply honestly, that is a split signal, not an invitation to pick the larger one.

## When to split

| Signal | Threshold |
| --- | --- |
| Size | More than roughly 400 changed lines or 15 files, excluding lockfiles, generated output, and vendored code. |
| Mixed concerns | Two or more of feature, refactor, bug fix, formatting, dependency bump, or config change in one branch. |
| Unrelated subsystems | Areas that need not ship together and share no reviewer. |
| Review load | A reviewer would have to hold more than one mental model at once to judge it. |

**How to propose a split.** Order by dependency: mechanical and reversible first (rename, extract, dependency bump), behavior change last. Every piece must build, pass tests, and be independently mergeable — a split that leaves a broken intermediate is worse than no split. Give each piece its subject line and one sentence of scope. When the work is already committed, propose new branches with the commits to cherry-pick, never a history rewrite of the existing branch unless the user asks for one.

## Resources

- [`references/pre-publish-checks.md`](references/pre-publish-checks.md) — what to scan for in the diff and working tree, each with what does not count, plus the full list of publication surfaces. Read in step 2.
- [`assets/handoff-output.md`](assets/handoff-output.md) — the handoff skeleton. Fill in step 6.
- [`references/hard-gate-hook.md`](references/hard-gate-hook.md) — a `PreToolUse` hook that enforces this gate at the tool layer. Read only when the user wants enforcement rather than policy.

## Validation

- No command run in the session staged, committed, pushed, tagged, published, deployed, or posted anything.
- The proposed format matches the convention observed in `git log`, and the handoff says which one it matched.
- The working tree and the remote are in the same state as when the task started, apart from file edits the work itself required.
- The handoff names every command the user needs to run, in order, with explicit paths rather than `-A` or `.`.
- The closing statement lists what was not done.

## Constraints

- Judging whether the work is correct is `fresh-eyes-review`. This skill assumes the work is finished and covers only how it is handed over.
- Reviewing a pull request someone else opened is `pr-review`.
- Do not gate reading, and do not narrate read-only inspection as though it needed permission. A gate that also blocks `git log` gets switched off within a day.
- **Plan approval is not commit approval.** Approving a plan covers the code, not its publication.
- Subagents inherit this gate. The `use-subagents` policy already keeps version control in the parent; this skill keeps it with the user.
