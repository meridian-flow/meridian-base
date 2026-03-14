# meridian-agents

External agent and skill content for Meridian.

## Layout

- `agents/*.md`: installable agent profiles
- `skills/*/SKILL.md`: installable skills

Meridian discovers this repo by layout only. No source manifest is required.

## Core Bootstrap Set

These files are the minimal bootstrap content Meridian needs to auto-install on demand:

- `agents/__meridian-orchestrator.md`
- `agents/__meridian-subagent.md`
- `skills/__meridian-orchestrate/SKILL.md`
- `skills/__meridian-spawn-agent/SKILL.md`

## Optional Workflow Content

This repo also carries the richer development workflow content used by Meridian itself, including:

- `dev-orchestrator`
- `coder`
- `reviewer*`
- `unit-tester`
- `smoke-tester`
- `documenter`
- `dev-workflow`
- `reviewing`
- `issue-tracking`
- `design`
- `plan-implementation`
- `work-coordination`

The current content is intentionally practical first. Names, prompts, and skill boundaries can be recalibrated after the default bootstrap path is working end to end.
