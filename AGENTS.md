# Repository Instructions

## Project Snapshot
- This repo stores reusable context-engineering skills for coding agents.
- Skills live under `skills/<skill-name>/`.
- Each skill must include `SKILL.md`; `agents/openai.yaml` is preferred for Codex UI metadata.

## Skill Rules
- Keep `SKILL.md` concise and action-oriented.
- Put trigger conditions in the frontmatter `description`; do not hide trigger rules only in the body.
- Do not add README, changelog, installation guide, or quick-reference files inside a skill folder.
- Add `scripts/`, `references/`, or `assets/` only when the skill needs them.
- Use lowercase hyphen-case skill names and match the folder name to the frontmatter `name`.

## Validation
- Validate changed skills with:

```bash
python3 /Users/zxch3n/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/<skill-name>
```

- Re-read generated `agents/openai.yaml` after changes and keep it aligned with the skill.

## Agent Workflow
- Prefer `rg` and `rg --files` for repository inspection.
- Preserve unrelated user changes.
- Keep repository-level docs thin; this repo is a skill catalog, not a documentation site.
