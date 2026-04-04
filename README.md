# meridian-base

Core coordination primitives for [Meridian](https://github.com/haowjy/meridian-channel).
This is what turns a bare `meridian` install into an orchestration system —
the orchestrator agent, the default subagent, and the skills that teach them
how to spawn work, track state, and coordinate across sessions.

Use this package to populate `.agents/` in projects that run Meridian.

## What You Get

An orchestrator that can do this out of the box:

```bash
# Break work into subtasks and delegate
meridian spawn -m codex -p "Implement the data model" -f plan/phase-1.md

# Run tasks in parallel (each blocks until done; harness runs them concurrently)
meridian spawn -m codex -p "Phase 2a: API endpoints"
meridian spawn -m codex -p "Phase 2b: CLI handlers"

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
| `__meridian-spawn` | The `meridian spawn` CLI — launching, waiting, parallel execution, reading reports |
| `__meridian-work-coordination` | Work item lifecycle — creating, switching, updating status, placing artifacts |
| `__meridian-session-context` | Mining past sessions — reading transcripts, searching decisions, finding related work |
| `__mars` | Agent package management via `meridian mars ...` — adding, syncing, linking, validating |
| `__meridian-diagnostics` | Diagnosing problems — failed spawns, corrupt state, harness issues |

## Install Into a Project

```bash
meridian mars init
meridian mars add @haowjy/meridian-base
meridian mars sync
```

If `mars.toml` already exists, you can skip `meridian mars init`.

## Layout

```
agents/*.md              # Agent profiles (YAML frontmatter + markdown)
skills/*/SKILL.md        # Skills (with optional resources/ subdirectory)
```

Meridian discovers these by layout convention — no manifest needed.

## See Also

- [meridian-channel](https://github.com/haowjy/meridian-channel) — the Meridian coordination engine
- [meridian-dev-workflow](https://github.com/haowjy/meridian-dev-workflow) — opinionated dev team built on top of this base
