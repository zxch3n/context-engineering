# context-engineering

Reusable agent skills for setting up durable project context.

[![skills.sh](https://skills.sh/b/zxch3n/context-engineering)](https://skills.sh/zxch3n/context-engineering)

## Install

Install all skills from this repository:

```bash
npx skills add zxch3n/context-engineering
```

Install only the context setup workflow:

```bash
npx skills add zxch3n/context-engineering --skill init-context-engineering
```

Install globally for Codex:

```bash
npx skills add zxch3n/context-engineering --skill init-context-engineering -g -a codex
```

## Use Without Installing

Generate a prompt for the skill without adding it to an agent:

```bash
npx skills use zxch3n/context-engineering@init-context-engineering
```

Start a supported agent with the generated prompt:

```bash
npx skills use zxch3n/context-engineering --skill init-context-engineering --agent codex
```

## Skills

| Skill | Description |
| --- | --- |
| `init-context-engineering` | Initialize or improve `AGENTS.md`, project commands, conventions, and reusable project-specific skills for future coding agents. |

## Source Formats

```bash
# GitHub shorthand
npx skills add zxch3n/context-engineering

# Full GitHub URL
npx skills add https://github.com/zxch3n/context-engineering

# Direct path to a skill
npx skills add https://github.com/zxch3n/context-engineering/tree/master/skills/init-context-engineering

# Local path
npx skills add ./context-engineering
```

## Layout

```text
skills/
  <skill-name>/
    SKILL.md
    agents/
      openai.yaml
```

Each skill is self-contained and follows the shared `SKILL.md` shape used by the open agent skills ecosystem.
