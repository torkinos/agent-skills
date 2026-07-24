# Skill Quality Checklist

Complete before finalizing, reviewing, or substantially revising a skill. Record evidence rather than checking boxes from intent alone.

## Purpose

- The skill has one coherent job, explicit non-goals, and clear boundaries with adjacent skills.
- 2-3 realistic use cases record inputs, expected outputs, and observable success criteria.
- 2-3 strong near misses name the correct adjacent skill or direct workflow.
- Changes to an existing skill cite reusable evidence (real tasks, recurring failures, user corrections) and are the smallest durable correction.
- Tool names, command flags, paths, and examples were verified against current reality, not copied from stale memory.

## Frontmatter and routing

- `name` is lowercase kebab-case, at most 64 characters, and exactly matches the directory.
- `description` is under 1024 characters: third-person capability + literal `Use this skill when` + material `Do not use` boundary.
- Routing focuses on user intent and realistic phrasing; no activation guidance leaks into the body.
- No unresolved sentinel, placeholder, or bare ellipsis remains anywhere.

## Body

- Critical constraints, mandatory resource loads, and validation appear early.
- The workflow is concrete enough to execute without guessing; defaults replace option menus.
- Bullets carry one instruction each; no paragraph-length bullets, filler, or repeated rationale.
- The main file is under roughly 150 lines; conditional detail lives in `references/`.

## Resources

- Every linked file exists at the exact relative path, one level deep, with a stated purpose and read/use/run condition.
- No vague "see references", empty directory, or unreferenced resource remains.
- Assets are reusable skeletons with all template sentinels resolved or intentionally retained (templates only).

## Scripts

- Each script is justified by repeated or fragile deterministic work.
- Scripts are non-interactive, support `--help`, use clear exit codes, and separate stdout data from stderr diagnostics.
- Destructive or stateful scripts are idempotent or gated by `--dry-run`/confirmation.
- No secrets, tokens, or machine-specific paths are embedded.

## Validation

- `node scripts/validate-skill-metadata.mjs skills/<name>` passes.
- `node scripts/validate-skill-links.mjs skills/<name>` passes.
- `npx -y skills-ref validate skills/<name>` passes; any external-validator quirk is named, not silently ignored.
- Added or changed scripts ran `--help` plus one representative safe execution.
- Final `git status`/diff shows only intended files.
