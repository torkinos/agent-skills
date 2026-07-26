# Agent Skills

Personal catalog of reusable Agent Skills for coding agents (Claude Code, Codex, Pi, and others).

## Available skills

- `create-skill` — create, review, and improve concise, actionable Agent Skills.
- `clean-as-you-go` — remove superseded and dead code as part of every change; never leave legacy behind.
- `use-subagents` — split, assign, isolate, verify, and clean up subagent lanes on any harness.
- `fresh-eyes-review` — review finished work with an independent agent, then triage the findings with reasons.
- `pr-review` — review a linked pull request and return prioritized, paste-ready comments; never posts.
- `commit-handoff` — never commit, push, or post without permission; hand back the commit message, PR title, and split.

## Structure

Each skill is self-contained under `skills/<name>/`:

```
skills/<name>/
├── SKILL.md          # frontmatter routing + post-activation contract
├── references/       # optional: detailed workflows, schemas, examples
├── assets/           # optional: reusable templates and output skeletons
└── scripts/          # optional: deterministic automation
```

## Install

Once pushed to GitHub (replace `OWNER` with the repository owner):

```sh
npx skills add OWNER/agent-skills --list
npx skills add OWNER/agent-skills --skill create-skill
```

## Validate

```sh
node scripts/validate-skill-metadata.mjs skills
node scripts/validate-skill-links.mjs README.md skills
npx -y skills-ref validate skills/create-skill
```

Run the validator test suites:

```sh
node --test scripts/*.test.mjs
```

## Credits

Repository structure, the validator scripts in `scripts/`, and the `create-skill`
and `use-subagents` skills are adapted from
[maxedapps/agent-skills](https://github.com/maxedapps/agent-skills) (MIT).

## License

MIT
