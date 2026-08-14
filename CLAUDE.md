# CLAUDE.md

This project's canonical agent instructions live in [AGENTS.md](./AGENTS.md) — read that first.
Everything below is Claude-Code-specific only; it does not replace or duplicate AGENTS.md.

## Claude-Code-specific notes
Skills (`interviewer`, `deployment`) live canonically under `.agents/skills/` so they stay
agent-agnostic. Claude Code only discovers skills under `.claude/skills/`, and skill
discovery reads each `SKILL.md`'s frontmatter literally (no file-import syntax works
there), so a symlink or a copy would be the only ways to bridge that — this repo does
neither. Instead, `.claude/` is entirely gitignored (see `.gitignore`) and each checkout
materializes its own local stub `.claude/skills/<name>/SKILL.md`: a real file carrying
the skill's `name`/`description` frontmatter (copied from the canonical file, so
discovery works) plus one line telling Claude to go read the canonical
`.agents/skills/<name>/SKILL.md` for the actual instructions. These stubs are local
scaffolding, not shared repo content, so they never get committed and never drift into a
second copy of the real content.

To (re)create them after a fresh checkout, for each skill under `.agents/skills/`, write
`.claude/skills/<name>/SKILL.md` as:

```markdown
---
name: <name>
description: >
  <copy the description: block verbatim from .agents/skills/<name>/SKILL.md>
---

This is a local, gitignored stub — not the skill's content. The canonical `<name>`
skill lives at `.agents/skills/<name>/SKILL.md`. Read that file in full and follow
it exactly; the frontmatter above exists only so Claude Code's `.claude/skills/` scanner
can discover and describe this skill.
```
