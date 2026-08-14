---
name: deployment
description: >
  Generates a fully populated provisioning/setup guide from
  templates/deployment_workflow_template.md (Tier 4 of this repo's Tiered Onboarding
  Framework — package-manager & proxy setup, environment creation, SSO auth, config
  symlinking) and writes it to output/output.md. Reuses org/platform details already
  captured by the interviewer skill when available, and only asks for whatever
  deployment-specific details are still missing. Use this whenever the user wants a
  setup guide, provisioning guide, deployment guide, "output.md", or instructions for
  standing up the coding-agent environment itself, or explicitly asks to run the
  deployment skill — even if they haven't run the interviewer skill first.
---

# Deployment Skill

You are generating Tier 4 of this repo's Tiered Onboarding Framework: the manual
provisioning guide that stands up the environment a harness will run in (see `AGENTS.md`
§ 0 — Tier 4 is operational provisioning, distinct from the behavioral rule tiers).

## Before asking anything

1. Read `templates/deployment_workflow_template.md` in full — its structure, section
   order, and every `[PLACEHOLDER]` are what you must reproduce, filled in, in the
   output. Treat it as a fill-in-the-blanks form, not a springboard to freelance new
   sections.
2. Look for a prior interview profile to reuse before asking the user anything:
   - `output/interview_profile.md` (written by the **interviewer** skill), and
   - any other `output/*.md` files already generated (`org_general.md`, `platform.md`,
     `team_preferences.md`, or a consolidated `AGENTS.md`/`GEMINI.md`).

   If any exist, read them and reuse every value they already establish (org name,
   platform type, proxy/mirror URLs, package manager, storage paths, etc.) — do not
   re-ask the user for something already on record.

## Filling in the gaps

`deployment_workflow_template.md` needs some placeholders an interview profile likely
won't have, since they're specific to provisioning rather than behavior — e.g. the
environment file name/format, dependency list, SSO login command, persistent-dir paths,
agent config directory name. Ask the user only for whatever placeholders are still
unresolved after checking `output/`, grouped into as few questions as possible and
offering multiple-choice options where sensible (matching the tone
`onboarding_interviewer.md` uses elsewhere in this framework).

If the user has no prior interview profile at all, ask for the full set `templates/deployment_workflow_template.md`
needs, but batch the questions rather than going placeholder-by-placeholder.

## Writing the output

- Never edit `templates/deployment_workflow_template.md` itself — it's the source of
  truth and stays generic/placeholder-based for every future run.
- Write the fully populated guide to `output/output.md`.
- Before finishing, verify there are no leftover `[PLACEHOLDER]`-style brackets in
  `output/output.md` (a quick `grep -n '\[[A-Z_]*\]' output/output.md` works). Any match
  means either a question was missed or a value is still unknown — resolve it, don't
  ship it with brackets in place.
- If a value is genuinely unknown and the user can't supply it, don't fabricate one
  (per the Honesty rules in `org_general_template.md`) — leave an explicit
  `[UNKNOWN: reason]` marker and call it out to the user directly instead of silently
  guessing or leaving a bare template placeholder.
