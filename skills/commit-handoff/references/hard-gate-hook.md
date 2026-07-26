# Hard gate: a PreToolUse hook

This skill is policy — it applies when the agent routes to it. A hook applies whether or not the agent read anything. Install this when you want the gate to hold on a turn where the skill never activated.

The hook returns `ask` rather than `deny`, so a genuine "yes, push it" is one keypress away and only the unattended case is stopped. Swap `ask` for `deny` to make it a hard block.

Schema below is from the Claude Code hooks documentation as of 2026-07.

## The script

Save as `~/.claude/hooks/gate-publish.sh` and `chmod +x` it. Requires `jq`.

```bash
#!/usr/bin/env bash
# PreToolUse hook: routes version-control and publishing commands to a permission
# prompt instead of letting them run unattended.
set -euo pipefail

cmd=$(jq -r '.tool_input.command // empty')
[ -n "$cmd" ] || exit 0

gated='(^|[[:space:];&|(])git[[:space:]]+(add|commit|push|tag|merge|rebase|cherry-pick|revert)([[:space:]]|$)'
gated+='|git[[:space:]]+reset[[:space:]]+--hard'
gated+='|git[[:space:]]+(branch[[:space:]]+-D|checkout[[:space:]]+-f|stash[[:space:]]+drop)'
gated+='|git[[:space:]]+push[[:space:]]+.*--force'
gated+='|gh[[:space:]]+(pr|issue|release|gist)[[:space:]]+(create|comment|review|merge|close|edit|ready|reopen|delete)'
gated+='|(npm|pnpm|yarn|cargo|poetry)[[:space:]]+publish|twine[[:space:]]+upload|docker[[:space:]]+push'
gated+='|(vercel|netlify|fly|wrangler|eas)[[:space:]]+(deploy|publish)'

if printf '%s' "$cmd" | grep -Eq "$gated"; then
  jq -n --arg r "commit-handoff: this writes to version control or publishes outward. Approve it explicitly, or ask for the handoff instead." \
    '{hookSpecificOutput: {hookEventName: "PreToolUse", permissionDecision: "ask", permissionDecisionReason: $r}}'
fi

exit 0
```

Matching the whole command string rather than its prefix is deliberate: `cd build && git push` has to be caught too. Read-only verbs are absent from the pattern on purpose — `git status`, `log`, `diff`, and `gh pr view` run untouched.

## The settings entry

In `~/.claude/settings.json` for every project, or `.claude/settings.json` for one:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "~/.claude/hooks/gate-publish.sh", "timeout": 10 }
        ]
      }
    ]
  }
}
```

The hook receives the tool call as JSON on stdin — `tool_name`, and for Bash the command string at `tool_input.command`. Exiting 0 with no output means no decision, so the normal permission flow applies.

`permissionDecision` accepts `allow`, `deny`, `ask`, and `defer`. `permissionDecisionReason` is shown to the agent. The alternative to the JSON form is exiting with code 2 and writing the reason to stderr, which blocks outright; the JSON form is used here because only it can return `ask`.

## What this does not do

Defense in depth, not a security boundary. It matches command strings, so it stops accidents, forgotten rules, and a context that never loaded this skill. It does not stop an agent that routes around it — through a shell script, an alias, a heredoc, an MCP tool, or a git wrapper that never spells out the verb. Treat it as a seatbelt, not a lock.

It also fires on every `Bash` call, so keep the pattern cheap; `jq` plus one `grep` is the budget.

To disable it deliberately, remove the entry from `settings.json`. To run a gated command yourself without involving the agent at all, type it in the prompt prefixed with `!`.
