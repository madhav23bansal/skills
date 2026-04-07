# Claude Code Skills

A collection of reusable Claude Code skills (slash commands) for DevOps, security, and development workflows.

## Usage

Add this directory to Claude Code:

```
/add-dir /path/to/this/repo
```

Then invoke any skill with `/<skill-name>`.

## Available Skills

| Skill | Command | Description |
|-------|---------|-------------|
| [Server Setup](server-setup/) | `/server-setup root@IP` | Interactive production server hardening (Ubuntu) |

## Adding a New Skill

1. Create a directory under the repo root: `my-skill/`
2. Add `SKILL.md` with frontmatter and instructions
3. Add supporting files (phases, templates, scripts) as needed
4. Update this README

### Skill Directory Structure

```
my-skill/
├── SKILL.md              # Required: main skill definition
├── phases/               # Optional: step-by-step phase files
│   ├── 01-first.md
│   └── 02-second.md
├── templates/            # Optional: output templates
└── scripts/              # Optional: helper scripts
```

### SKILL.md Frontmatter

```yaml
---
name: my-skill
description: What this skill does (max 250 chars, front-load the key use case)
disable-model-invocation: true    # true = only manual /invoke, false = Claude can auto-trigger
argument-hint: "[arg1] [arg2]"    # shown in autocomplete
allowed-tools: Bash Read          # restrict available tools
effort: high                      # low, medium, high, max
---
```

See [Claude Code Skills docs](https://docs.anthropic.com/en/docs/claude-code/skills) for full reference.
