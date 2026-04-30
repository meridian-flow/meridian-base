---
name: meridian-spawn
description: >
  Multi-agent coordination via the meridian CLI. Use this skill whenever you
  need to delegate work to another agent, run tasks in parallel, check on
  spawn progress, coordinate multiple agents, or inspect spawn outputs. Also
  use when you want to route work to a specific model or provider.
disable-model-invocation: true
allow_implicit_invocation: false
---

# meridian-spawn

## Core Loop

Spawns block by default. Use `--bg` to return immediately with a spawn ID, then `wait` to block until completion:

```bash
meridian spawn -a coder -p "Step A" --bg  # -> p101
meridian spawn -a coder -p "Step B" --bg  # -> p102
meridian spawn wait                        # blocks until both complete
```

### Output Discipline

Parse `spawn_id` and `status` from JSON responses in agent mode. Some commands return JSON for complex data, others return text — check per-command. Run `meridian <command> -h` for format details.

### Spawn Lifecycle

Spawns transition `queued` -> `running` -> `finalizing` -> `succeeded | failed | cancelled`. `finalizing` is transient — treat it as active when polling. `spawn wait` blocks through `finalizing` and returns only on a terminal state.

### Crash-Only Design

Writes are atomic (tmp+rename), reads tolerate truncation. Recovery is startup behavior. If a spawn dies mid-flight, `meridian doctor` or read-path reconciliation detects and reports it.

## Spawning

**`-a` (agent profile)** — use when a profile exists for the role:

```bash
meridian spawn -a reviewer -p "Review this change"
```

**`-m` (direct model)** — use for one-off tasks where no profile fits:

```bash
meridian spawn -m MODEL -p "Implement the fix"
```

Pipe from stdin or use `--prompt-file` for multi-paragraph prompts to avoid shell-quoting issues.

Pass reference files or directories with `-f` so the spawned agent starts with context. Scope tightly: 2-4 files is typical, six is heavy, ten means the handoff needs rethinking.

```bash
meridian spawn -a coder -p "Implement auth middleware" \
  -f src/middleware/ -f src/middleware/base.py -f design/phase-2.md
```

Pass prior conversation history with `--from` when the spawn needs reasoning that isn't materialized to files. `--from <spawn-id>` pulls a specific prior spawn's transcript. `--from $MERIDIAN_CHAT_ID` pulls the top-level primary session — available at any depth because `$MERIDIAN_CHAT_ID` is inherited by every descendant.

Run `meridian mars models list` for available models. Run `meridian mars list` for agent profiles and skills. Model preferences belong in agent profiles or `meridian config`, not hardcoded in spawn commands.

## Work Items

Attach spawns to a work item so they're grouped and traceable. Use `--desc` for human-readable labels.

```bash
meridian spawn -a agent --desc "Implement step 2" -p "..."
```

For work item lifecycle, see the `/meridian-work-coordination` skill.

## Parallel Spawns

Use `--bg` to launch without blocking, then `wait` to collect all results in one notification:

```bash
meridian spawn -a coder -p "Implement auth" --bg --desc "auth"
meridian spawn -a coder -p "Implement cache" --bg --desc "cache"
meridian spawn wait   # one notification when ALL complete
```

Launch all `--bg` spawns, then drain with `meridian spawn wait` at a barrier — before a final response, before starting dependent work, or when local work is exhausted.

When `meridian spawn wait` runs through a harness shell tool, poll sparsely. Wait yield timing is harness-aware: Claude 270s, Codex 900s. Use `--yield-after-secs` to override.

## Checking Status

Use the work dashboard for situational awareness:

```bash
meridian work
```

Reattach to spawns from a previous session with `meridian spawn wait` or `meridian spawn wait <spawn_id>`. Use `meridian spawn children <id>` to see what a spawn spawned.

## Steering a Running Spawn

**Inject before you cancel.** Course-correct a running spawn instead of killing and restarting — cancel destroys built-up context.

```bash
meridian spawn inject p107 --message "Use the existing adapter pattern in src/adapters/"
```

## When a Spawn Fails

Read the report via `meridian spawn show SPAWN_ID`. For deeper investigation, see [`../meridian-cli/resources/debugging.md`](../meridian-cli/resources/debugging.md).

## Shared Filesystem

Spawns share filesystem directories for exchanging data. Context directories (`$MERIDIAN_CONTEXT_*_DIR`) are injected into every agent's system prompt at launch.

See the `/meridian-work-coordination` skill for when to use which.

## Committing Spawn Changes

Use `meridian spawn files` to stage exactly what a spawn changed:

```bash
meridian spawn files p107 | xargs git add
meridian spawn files p107 -0 | xargs -0 git add   # paths with spaces
```

## Template Variables

Use `{{KEY}}` placeholders in prompts, replaced at launch time with `--prompt-var`:

```bash
meridian spawn -a coder \
  -p "Implement {{TASK}} following {{CONSTRAINT}}" \
  --prompt-var TASK=auth-refactor \
  --prompt-var CONSTRAINT="no direct DB access"
```

## Beyond the Basics

For continue/fork, cancel, stats, permission tiers, reports, and dry-run, see [`resources/advanced-commands.md`](resources/advanced-commands.md).
For troubleshooting strange behavior, see [`../meridian-cli/resources/debugging.md`](../meridian-cli/resources/debugging.md).
For project defaults (model, agent, permissions, timeouts), see [`../meridian-cli/resources/configuration.md`](../meridian-cli/resources/configuration.md).
