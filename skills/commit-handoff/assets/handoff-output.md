# Handoff output

Fill this in and give it to the user. Run none of it. The order matters: an exposure hit has to be visible before any commit message tempts a paste. Drop the split section when no threshold was hit, and drop the pull request lines when no pull request is in play.

```text
## Handoff — <one line on what the work delivered>

**Not done:** nothing staged, committed, pushed, tagged, published, or posted.
**Exposure check:** <clean | <n> items, each marked blocking or noted>
**Branch:** <current> → <suggested name, and one clause on why the change is needed>

### Commit

Convention: <the style matched, and where it came from — e.g. "Conventional Commits, already used across the last 20 commits">

    <subject line>

    <body, when the subject does not say everything>

    <BREAKING CHANGE: / Refs: footers, when they apply>

### Split — <threshold hit>

1. `<subject line>` — <files or scope, one sentence>
2. `<subject line>` — <files or scope, one sentence>

### Pull request

**Title:** <title, matching the repo's PR title style>

**Description:**

    ## What
    <what changed, in the reviewer's terms>

    ## Why
    <the problem this solves, and the decision behind the approach>

    ## How to verify
    <the steps a reviewer runs to see it working>

    ## Risk and rollback
    <what could break, and what reverting leaves behind>

### Commands to run

    git checkout -b <branch>
    git add <explicit paths — never -A or .>
    git commit -m "<subject>"
    git push -u origin <branch>
    gh pr create --title "<title>" --body-file <path>
```

`git add` takes explicit paths, always. A pasted `git add -A` is exactly how an untracked `.env` or a scratch file reaches a remote, and the pre-publish scan cannot protect against a command that widens its own scope.

When the exposure check finds a blocking item, the handoff ends after that section. No commit message, no commands, until the user has decided what to do about it.
