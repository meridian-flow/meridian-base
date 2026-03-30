# meridian-base

Core coordination primitives for [Meridian](https://github.com/haowjy/meridian-channel).
This is what turns a bare `meridian` install into an orchestration system —
the orchestrator agent, the default subagent, and the skills that teach them
how to spawn work, track state, and coordinate across sessions.

Meridian auto-bootstraps these agents when they're missing. You don't need to
install this repo explicitly unless you want the full set of skills (session
context mining, troubleshooting, install management).

## What You Get

An orchestrator that can do this out of the box:

```bash
# Break work into subtasks and delegate
meridian spawn -m codex -p "Implement the data model" -f plan/phase-1.md

# Run tasks in parallel
meridian spawn -m codex -p "Phase 2a: API endpoints"
meridian spawn -m codex -p "Phase 2b: CLI handlers"
meridian spawn wait p2 p3

# Track work items across sessions
meridian work start "auth-refactor"
meridian work update --status implementing

# Search past context
meridian session search "auth design decision"
meridian report search "token expiry"
```

These capabilities come from the skills below — they're injected into the
orchestrator's system prompt so the agent knows how to use meridian's CLI.

## Agents

| Agent | Model | Purpose |
|---|---|---|
| `__meridian-orchestrator` | (configured default) | Plans, delegates, and evaluates subagent work. Loaded with coordination skills. |
| `__meridian-subagent` | gpt-5.3-codex | Default execution agent for scoped tasks. Receives a prompt, does the work, reports back. |

## Skills

| Skill | What it teaches the agent |
|---|---|
| `__meridian-orchestration` | How to break work into subtasks, pick models, run review cycles, and iterate |
| `__meridian-spawn` | The `meridian spawn` CLI — launching, waiting, parallel execution, reading reports |
| `__meridian-work-coordination` | Work item lifecycle — creating, switching, updating status, placing artifacts |
| `__meridian-session-context` | Mining past sessions — reading transcripts, searching decisions, finding related work |
| `__meridian-install` | Managing agent/skill sources — installing, updating, resolving dependencies |
| `__meridian-diagnostics` | Diagnosing problems — failed spawns, corrupt state, harness issues |

## Bootstrap

Meridian auto-installs the orchestrator, subagent, and their skill dependencies
when they're missing locally. This repo is the well-known bootstrap source — if
no provenance is found, Meridian adds it to `agents.toml` automatically.

You never need to think about this unless you're debugging install issues.

## Install

```bash
meridian sources install @haowjy/meridian-base
```

## Layout

```
agents/*.md              # Agent profiles (YAML frontmatter + markdown)
skills/*/SKILL.md        # Skills (with optional resources/ subdirectory)
```

Meridian discovers these by layout convention — no manifest needed.

## See Also

- [meridian-channel](https://github.com/haowjy/meridian-channel) — the Meridian coordination engine
- [meridian-dev-workflow](https://github.com/haowjy/meridian-dev-workflow) — opinionated dev team built on top of this base
