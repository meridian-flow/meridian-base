# meridian-base

Core coordination primitives for [Meridian](https://github.com/meridian-flow/meridian-cli).
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
| `meridian-default-orchestrator` | (configured default) | Plans, delegates, and evaluates subagent work. Loaded with coordination skills. |
| `meridian-subagent` | gpt-5.3-codex | Default execution agent for scoped tasks. Receives a prompt, does the work, reports back. |

## Skills

| Skill | What it teaches the agent |
|---|---|
| `meridian-cli` | Meridian and Mars CLI mental model — command-surface discovery, diagnostics patterns, sessions, and core principles |
| `meridian-spawn` | The `meridian spawn` CLI — launching, waiting, parallel execution, reading reports |
| `meridian-work-coordination` | Work item lifecycle — creating, switching, updating status, placing artifacts |

## Install Into a Project

```bash
meridian mars init
meridian mars add @meridian-flow/meridian-base
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

- [meridian-cli](https://github.com/meridian-flow/meridian-cli) — the Meridian coordination engine
- [meridian-dev-workflow](https://github.com/meridian-flow/meridian-dev-workflow) — opinionated dev team built on top of this base
