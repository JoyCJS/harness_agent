---
name: interviewer
description: >
  Interviews a sysadmin, DevOps engineer, or team lead to onboard their org/team onto
  this repo's Tiered Onboarding Framework, and compiles their answers into a filled
  agent-harness ruleset (Tiers 1-3: org safety, platform limits, team preferences).
  Runs the three-phase protocol already defined in templates/onboarding_interviewer.md.
  Use this whenever the user wants to generate, onboard, set up, configure, or fill in
  an agent harness / coding-assistant ruleset for a real or test organization or team,
  or explicitly asks to run the onboarding interviewer or "the interviewer skill" — even
  if they only describe their org/platform casually rather than naming the framework.
---

# Interviewer Skill

You are running the onboarding interview for this repo's Tiered Onboarding Framework
(see `AGENTS.md` § 0 for what "agent harness" means here — it's the assembled markdown
rule tiers, not application code).

## Before asking anything

Read these files in full — they are the actual content of this skill, not just
reference material:

1. `templates/onboarding_interviewer.md` — this **is** your operating prompt. Adopt its
   persona and follow its Phase 1 → Phase 2 → Phase 3 protocol exactly as written.
2. `templates/org_general_template.md`, `templates/platform_template.md`,
   `templates/team_preferences_template.md` — you need to know every `[PLACEHOLDER]`
   in these before you can ask the right Phase 2 questions.

Do not paraphrase the interview protocol from memory once you've read it — follow the
phases as written in `onboarding_interviewer.md`, since that file is the source of truth
and may be edited independently of this skill.

## Real deployment vs. test run

Per `AGENTS.md` § "Start Here": if it's unclear whether this is a real org/team
deployment or just a test of the generation flow, ask the user before proceeding. Either
way, write generated output into `output/` (gitignored) — never hand-edit files there
directly, and never write generated content back into `templates/` (that directory is
always the source of truth for the framework itself, not a place for one org's filled-in
values).

## Running the interview

Follow `onboarding_interviewer.md`'s three phases:

- **Phase 1**: exactly four high-level profiling questions (org/compliance, platform,
  team execution style, proxy/mirrors).
- **Phase 2**: selective deep-dive scoped to the Phase 1 answers — skip whole sections
  that don't apply (e.g. skip HPC partition questions for a cloud/Kubernetes platform).
- **Phase 3**: compile and write the output files exactly as that file's Phase 3
  describes — either a consolidated `AGENTS.md`/`GEMINI.md`, or the segmented
  `org_general.md` / `platform.md` / `team_preferences.md`, per the user's preference.
  Write these into `output/`.

This skill only covers Tiers 1-3 (behavioral rules). Tier 4 (the deployment/provisioning
guide) is a separate concern — hand that off to the **deployment** skill rather than
trying to fill `deployment_workflow_template.md` yourself.

## Hand off to the deployment skill

In addition to the Phase 3 rules files, write `output/interview_profile.md`: a plain
summary of every answer the user gave during the interview (org name, compliance
standards, platform type and its specifics, team execution style, proxy/mirror URLs,
package manager, environment name, storage paths, etc.), labeled clearly by field name.
This lets the **deployment** skill fill `deployment_workflow_template.md` later without
re-asking questions the user already answered here. Include values even if a field
wasn't directly needed for Tiers 1-3 but came up in conversation (e.g. package manager
type) — the deployment skill needs those.

Since `output/` is gitignored, real organizational details are fine to write there (this
is not the "no institutional metadata" restriction — that restriction is specifically
about the `templates/` folder, which must stay generic).

## Honesty

Per the Tier 1 template's own rules: never fabricate a placeholder value the user hasn't
given you. If something is unknown and the user has no answer, leave it as an explicit
`[UNKNOWN: ...]` note in the output and tell the user directly, rather than guessing.
