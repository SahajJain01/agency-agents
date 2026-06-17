---
name: agency-agents
description: >-
  Find, install, and activate The Agency's 220+ specialized AI agent
  personalities (engineering, design, security, GIS, finance, marketing, sales,
  product, testing, strategy, and more) as Claude Code subagents. Use when the
  user wants to pick the right specialist for a task, install agents into
  ~/.claude/agents, list the available agents or divisions, or adopt one of the
  agent personas inline.
---

# The Agency — Agent Personalities for Claude Code

This repository ("The Agency") is a curated library of **220 specialized AI
agent personality files** organized into **17 divisions**. Each file is a
self-contained persona — identity, mission, workflows, deliverables, and
communication style — that can run as a Claude Code subagent or be adopted
inline in a conversation.

Use this skill to help the user **discover**, **install**, and **activate**
these agents from within Claude Code Desktop.

## Repository layout

- Agent files live at `<division>/<division>-<slug>.md` (e.g.
  `engineering/engineering-frontend-developer.md`).
- Each file starts with YAML frontmatter: `name`, `description`, `color`,
  `emoji`, `vibe`, followed by the persona body.
- `divisions.json` (repo root) is the **source of truth** for the division
  list — read it for labels, icons, and colors. Do not hand-maintain a
  separate division list.
- `scripts/install.sh` installs agents into agentic tools; `scripts/convert.sh`
  regenerates the per-tool files under `integrations/` (Copilot, Cursor,
  Gemini CLI, etc.). For Claude Code you only need `install.sh`.

The 17 divisions: `academic`, `design`, `engineering`, `finance`,
`game-development`, `gis`, `marketing`, `paid-media`, `product`,
`project-management`, `sales`, `security`, `spatial-computing`, `specialized`,
`strategy`, `support`, `testing`.

## 1. Discover — recommend the right agent

When the user describes a task and asks who should handle it:

1. Map the task to one or more likely divisions (use `divisions.json` for the
   labels).
2. Search that division's files. Prefer reading frontmatter `name` +
   `description` rather than whole files:
   - `grep -rh "^description:" engineering/` to scan one division, or
   - `./scripts/install.sh --list agents` to print every agent slug, or
   - `./scripts/install.sh --list teams` to see divisions and agent counts.
3. Recommend the best 1–3 matches by name and the path to each file, with a
   one-line reason. Open the file to confirm the persona fits before asserting
   it does.

## 2. Install — copy agents into Claude Code

Agents become Claude Code subagents once copied into `~/.claude/agents/`.

```bash
# All 220 agents
./scripts/install.sh --tool claude-code

# Just one or more divisions
./scripts/install.sh --tool claude-code --division engineering,security

# Specific agents by slug
./scripts/install.sh --tool claude-code --agent frontend-developer,code-reviewer

# Preview without writing anything
./scripts/install.sh --tool claude-code --dry-run

# Symlink instead of copy, so repo updates propagate automatically
./scripts/install.sh --tool claude-code --link
```

Notes:
- `install.sh` and `convert.sh` are **bash** scripts. On Windows run them from
  **Git Bash** or **WSL**, not PowerShell.
- Override the destination with the `CLAUDE_CONFIG_DIR` env var or `--path`.
- Manual alternative (no script): copy the files yourself, e.g.
  `cp engineering/*.md ~/.claude/agents/`.
- After installing, the user must restart / reopen the Claude Code session for
  new subagents to be picked up.

## 3. Activate

**As a subagent (after install):** the agent appears as a selectable agent type
and can be dispatched with the Agent/Task tool for that persona.

**Inline (no install needed):** read the chosen agent file and adopt its
persona for the rest of the task — follow its identity, mission, and
communication style. This is the fastest way to "try" an agent.

## Conventions when editing or adding agents

If the user wants to author or modify an agent, keep the repo's invariants
intact:
- Filename pattern `<division>/<division>-<slug>.md` with the standard
  frontmatter keys.
- Adding a **new division** requires more than a directory: update
  `divisions.json`, then run `scripts/check-divisions.sh` and fix everywhere it
  points (the `AGENT_DIRS` arrays in `scripts/convert.sh` and
  `scripts/lint-agents.sh`, plus the CI path filters). CI fails if these
  disagree.
- Validate with `scripts/lint-agents.sh` and read `CONTRIBUTING.md` before
  opening a PR upstream.
