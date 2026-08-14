# CLAUDE.md

This project's canonical agent instructions live in [AGENTS.md](./AGENTS.md) — read that first.
Everything below is Claude-Code-specific only; it does not replace or duplicate AGENTS.md.

## Claude-Code-specific notes
Skills (`interviewer`, `deployment`) live canonically under `.agents/skills/` so they stay
agent-agnostic. Claude Code only discovers skills under `.claude/skills/`, so
`.claude/skills/interviewer` and `.claude/skills/deployment` are symlinks into
`.agents/skills/` — Claude-Code-specific plumbing, not a second copy of the content.
