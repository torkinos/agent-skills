---
name: create-skill
description: >-
  Creates, reviews, and improves Agent Skills that are concise, unambiguous,
  and actionable. Use this skill when authoring a new SKILL.md, revising an
  existing skill's routing description, body, references, assets, or scripts,
  or reviewing a skill for quality. Do not use for installing third-party
  skills or for encoding one-off preferences without reusable evidence.
license: MIT
metadata:
  short-description: Create and review concise Agent Skills
---

# Create Skill

## Critical rules

- All activation guidance lives in the frontmatter `description`; the body loads only after activation.
- Description format: `[Third-person capability]. Use this skill when [triggers]. Do not use for [material near misses].` Keep it under 1024 characters and free of placeholders.
- `name` is lowercase kebab-case, under 64 characters, and exactly matches the skill directory.
- Keep `SKILL.md` compact and scannable: short headings, focused bullets, numbered procedures. Put critical constraints, mandatory resources, and validation early. Target under 150 lines.
- Progressive disclosure: `references/` for detailed workflows, schemas, and examples; `assets/` for reusable templates and output skeletons; `scripts/` for repeated deterministic operations. Keep resources one level deep.
- Link a resource only after its file exists, with its exact path, purpose, and read/use/run condition.
- Ground every change in real tasks, recurring failures, or user corrections. Prefer the smallest durable fix; reject speculative additions.

## Workflow

1. **Define use cases.** Record 2-3 realistic tasks with inputs, expected output, and success criteria. Record 2-3 near misses and name the correct adjacent skill or direct workflow for each.
2. **Check the ecosystem.** Read existing skills in this catalog for overlap, reusable patterns, and duplicated policy. Do not broaden scope because related guidance exists.
3. **Draft the frontmatter first.** Write the `description` with realistic user phrasing before writing any body content.
4. **Write the body.** Start from `assets/skill-template.md`: replace every sentinel, delete unused sections. Include only post-activation rules: constraints, sequence, validation, failure handling. Then compress — remove throat-clearing, repeated rationale, and content that does not change execution.
5. **Add resources only when warranted.** Create `references/`, `assets/`, or `scripts/` files when detail is reusable or would crowd the main file. Make scripts non-interactive with `--help`, clear exit codes, and safe defaults; require `--dry-run` or confirmation for destructive ones.
6. **Evaluate routing.** Judge 3-5 realistic should-trigger prompts and 2-3 near misses against the description alone. Revise only evidence-backed gaps.
7. **Validate.** Complete `references/skill-quality-checklist.md`, then run the checks below.

## Resources

- [`assets/skill-template.md`](assets/skill-template.md) — starter for new skills. Use in step 4; replace every `REPLACE_ME` and delete unused sections before validation.
- [`references/skill-quality-checklist.md`](references/skill-quality-checklist.md) — quality gate. Complete in step 7 before finalizing or reviewing any skill; record evidence, not intent.

## Validation

Run from the repository root:

```sh
node scripts/validate-skill-metadata.mjs skills/<name>
node scripts/validate-skill-links.mjs skills/<name>
npx -y skills-ref validate skills/<name>
```

- All three must pass; report any skipped check and its reason.
- Run each added or changed script's `--help` and one representative safe execution.
- Never claim resources, checks, or artifacts that do not exist.
