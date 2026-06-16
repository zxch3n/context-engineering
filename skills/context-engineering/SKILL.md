---
name: context-engineering
description: Initialize or improve durable project context for coding agents. Use when a user asks to set up context engineering in a repository, create or repair AGENTS.md, document agent workflows, capture project commands and conventions, add project-specific skills, or make a repo easier for future agents to work in safely and efficiently.
---

# Context Engineering

## Goal

Turn a repository into a reliable working surface for future agents. Prefer a small set of durable, verified context files over long prompt dumps or stale documentation.

## Workflow

1. Inspect the existing project before writing context.
   - Read root and nested `AGENTS.md` files, README files, package manifests, build configs, test configs, CI workflows, and obvious scripts.
   - Use `rg --files` first to map the repo.
   - Treat uncommitted user changes as owned by the user.

2. Identify the smallest useful context surface.
   - Use root `AGENTS.md` for repository-wide instructions.
   - Use nested `AGENTS.md` only when a subtree has different rules.
   - Use `skills/<skill-name>/SKILL.md` or `.agents/skills/<skill-name>/SKILL.md` only for reusable workflows that future agents should explicitly invoke.
   - Use extra docs only when the information is too large or too specialized for `AGENTS.md`.

3. Write context that future agents can trust.
   - State verified commands for install, build, lint, test, typecheck, format, and local run.
   - Record project-specific architecture, ownership boundaries, naming rules, generated files, and unsafe operations.
   - Capture user or team preferences that affect implementation choices.
   - Keep instructions imperative and concrete.
   - Mark uncertain information as unverified instead of presenting it as fact.

4. Keep context lean.
   - Do not repeat obvious tool knowledge or generic coding advice.
   - Do not include secrets, credentials, private tokens, or machine-local paths unless the repo requires them and the user approves.
   - Do not create broad process documents when a short `AGENTS.md` section is enough.
   - Remove TODO placeholders before finishing.

5. Add project-specific skills only when they reduce repeated work.
   - Create one skill per reusable workflow.
   - Put the trigger conditions in the skill frontmatter `description`.
   - Keep `SKILL.md` under 500 lines when possible.
   - Put detailed variant notes in directly linked `references/` files only when needed.
   - Validate each skill with the available skill validator.

6. Validate the result.
   - Run existing repo checks when they are known and safe.
   - For skills, run the skill validator against each changed skill folder.
   - Re-read the final files and verify they match the discovered repo facts.
   - Summarize what context was added and which commands were verified.

## AGENTS.md Shape

Use this shape unless the repo already has a stronger local pattern:

```markdown
# Repository Instructions

## Project Snapshot
- State what this repo is for in one or two bullets.
- Name the main languages, package managers, frameworks, and important directories.

## Commands
- Install:
- Build:
- Test:
- Lint:
- Format:
- Run locally:

## Working Rules
- State code style, ownership boundaries, generated files, and forbidden edits.
- State how to handle migrations, schema changes, fixtures, snapshots, or release files.

## Agent Workflow
- State how agents should inspect, edit, validate, and report changes in this repo.
```

Only keep command rows that are real for the project. If a command cannot be verified, say so briefly.
