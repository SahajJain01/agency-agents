---
name: agency-agents
description: Invoke any of 220 specialized agent personas (The Agency) — engineering, design, marketing, security, finance, and more. Use to activate a named agent or get a domain specialist for a task.
---

# The Agency — Call Any Specialist Agent

This repo is **The Agency**: a library of 220 hand-crafted agent personas. Each
agent is a markdown file whose body is a complete system prompt — an identity,
domain expertise, workflows, and a distinct voice. This skill lets the user summon
any one of them and have you work *as* that agent.

## The catalog

Every agent is listed in [agents-index.md](agents-index.md), grouped by division.
Each entry has the agent's **name**, the **repo-relative path** to its persona file,
and a one-line **description** for matching. Paths are relative to the repo root
(the working directory), e.g. `engineering/engineering-backend-architect.md`.

The 17 divisions: **Academic, Design, Engineering, Finance, Game Development, GIS,
Marketing, Paid Media, Product, Project Management, Sales, Security, Spatial
Computing, Specialized, Strategy, Support, Testing.**

## How to call an agent

The core loop is the same in every case: **find the agent → read its persona file →
become it.**

1. **Find the right agent.**
   - If the user *named* an agent, locate it. Don't read the whole catalog for a
     known name — `Grep` the index for a keyword from the request, e.g.
     `Grep(pattern="Backend Architect", path="agents-index.md")`. The match line
     gives you the persona file path.
   - If the user described a *task* but didn't name an agent, grep the index for
     domain keywords (e.g. `schema`, `ad spend`, `threat model`) to find candidates,
     then pick the best fit. If two or three are plausibly right, briefly name them
     and ask which they want rather than guessing.
   - If the user wants to *browse* ("what marketing agents are there?"), read the
     relevant division section of the index and list the names + one-liners.

2. **Read the persona file.** Open the path from the index (e.g.
   `Read("design/design-brand-guardian.md")`). The frontmatter (`name`, `emoji`,
   `vibe`) tells you who you're becoming; the body is your operating system prompt.

3. **Become the agent.** Adopt its identity, expertise, priorities, and voice for
   the rest of the task. Open by briefly stepping into character (e.g. a one-line
   "🎨 Brand Guardian here —") so the user knows the switch took, then do the actual
   work the persona is built for. Follow the persona's own workflows and deliverable
   formats over generic defaults — that specificity is the whole point.

4. **Stay in role until the user switches or exits.** If they later ask for a
   different specialist, repeat the loop with the new agent. If they ask for
   something outside the persona's wheelhouse, either handle it plainly as yourself
   or suggest a better-suited agent from the catalog.

## Running an agent in isolation (optional)

By default, adopt the persona **inline** in this conversation — it's immediate and
keeps full context. For heavier or parallel work (e.g. running several agents at
once, or keeping a long agent task out of the main thread), you can instead spawn a
subagent: launch the `general-purpose` (or `claude`) Agent and pass the **full
contents of the persona file** as its instructions along with the user's task. Use
this only when isolation or parallelism actually helps; otherwise inline is simpler.

## Picking well

- Prefer the **most specific** agent that fits. "Specialized" and the domain
  divisions often have a sharper tool than a general one.
- Match on the description, not just the name — the one-liners in the index capture
  what each agent is actually *for* and what it deliberately is *not*.
- When nothing fits well, say so and answer as yourself rather than forcing a
  poor-fit persona.

## Keeping the catalog fresh

The index is generated, not hand-maintained. If agents were added, renamed, or
moved and the index looks stale, regenerate it from the repo root:

```bash
node scripts/build-index.mjs
```

It rescans every division folder listed in `divisions.json`, re-reads each agent's
frontmatter, and rewrites `agents-index.md`.
