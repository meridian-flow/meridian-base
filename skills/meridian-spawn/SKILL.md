---
name: meridian-spawn
description: >
  Multi-agent coordination via the meridian CLI. Use this skill whenever you
  need to delegate work to another agent, run tasks in parallel, check on
  spawn progress, coordinate multiple agents, or inspect spawn outputs. Also
  use when you want to route work to a specific model or provider.
---

# meridian-spawn

## Core Loop

See `meridian-cli` for output mode discipline and JSON parsing expectations.

Spawns block by default. Use `--bg` to return immediately with a spawn ID, then `wait` to block until completion:

```bash
# Launch in background (returns spawn ID immediately)
meridian spawn -a coder -p "Step A" --bg  # → p101
meridian spawn -a coder -p "Step B" --bg  # → p102

# Wait for all at once
meridian spawn wait p101 p102
```

## Spawning

Two ways to spawn, depending on whether you want a reusable configuration or a one-off:

**`-a` (agent profile)** — use when a profile exists for the role. The profile encodes model, system prompt, skills, and permissions, so you don't repeat yourself across spawns:

```bash
meridian spawn -a reviewer -p "Review this change"
```

**`-m` (direct model)** — use for one-off tasks where no profile fits, or when you want a specific model without the rest of a profile's configuration:

```bash
meridian spawn -m MODEL -p "Implement the fix"
```

You can combine both to override a profile's default model, but you usually shouldn't — the profile author already chose the right model for the role:

```bash
meridian spawn -a reviewer -m sonnet -p "Quick review"
```

Long inline prompts hit shell-quoting bugs faster than you'd expect. Backticks, dollar signs, unbalanced quotes, zsh history expansion, and heredoc edge cases can silently corrupt the prompt. For anything multi-paragraph or containing code, pipe from stdin (`cat prompt.md | meridian spawn ...`) or pass `--prompt-file`. Both avoid shell parsing entirely, so the prompt bytes are read exactly as written. Reserve inline `-p` for short literal prompts.

Use the prompt input mode that matches prompt complexity:

```bash
# Short inline
meridian spawn -a reviewer -p 'review the auth diff'

# Stdin pipe (for long prompts)
cat prompt.md | meridian spawn -a coder

# Explicit file flag
meridian spawn -a coder --prompt-file plan/phase-2.md
```

For work-scoped files, query `meridian work current` for the path and use it literally.

Pass reference files or directories with `-f` so the spawned agent starts with the context it needs instead of exploring from scratch. Files are included as full content; directories are included as a tree listing so the agent sees the structure and can read what it needs. Combine both — folder for the map, specific files for the content that matters:

```bash
# Folder gives structure, specific files give content
meridian spawn -a coder -p "Implement auth middleware" \
  -f src/middleware/ \
  -f src/middleware/base.py \
  -f design/phase-2.md
```

Pass prior conversation history with `--from` when the spawn needs reasoning that isn't materialized to files. `--from <spawn-id>` pulls a specific prior spawn's transcript. `--from $MERIDIAN_CHAT_ID` pulls the top-level primary session — the root conversation at the top of the chat tree, available at any spawn depth because `$MERIDIAN_CHAT_ID` is inherited by every descendant. Use the env-var form when a spawned descendant needs the primary's original framing, decisions, or context:

The env-var form supplies framing and rationale; concrete scope still comes from `-f` files and the prompt:

```bash
# Top-level conversation gives framing; plan file and source dir give scope
meridian spawn -a coder \
  --from $MERIDIAN_CHAT_ID \
  -f plan/phase-2.md \
  -f src/auth/ \
  -p "Implement phase 2 per the approach discussed at the primary"

# Pull a specific prior spawn's reasoning
meridian spawn -a reviewer --from p203 -f design/auth.md -p "Review against design intent"
```

Run `meridian models list` to see available models with their strengths and cost tiers. Run `meridian models -h` for the full model management surface. Run `meridian mars list` to see available agent profiles and skills. Model and agent preferences belong in your project's agent profiles, `meridian config`, or project docs (CLAUDE.md, AGENTS.md) — hardcoding them into spawn commands makes them invisible to `meridian config show`, impossible to change project-wide, and silently divergent from profile defaults.

To create or edit agent profiles, load the `agent-creator` skill. To create or edit skill files, load the `skill-creator` skill.


## Work Items

Attach spawns to a work item so they're grouped on the dashboard and traceable later. Without a work item, spawns are orphaned IDs that are hard to find or make sense of after the fact.

```bash
# Spawns automatically inherit the active work item
meridian spawn -a agent --desc "Implement step 2" -p "..."

# Or attach explicitly (useful for automation or cross-cutting tasks)
meridian spawn -a reviewer --work auth-refactor --desc "Review step 1" -p "..."
```

Use `--desc` to give spawns a human-readable label — it shows up in `meridian work` and `spawn list`, so you're not staring at bare spawn IDs.

For work item lifecycle (creating, switching, updating, completing, and dashboard), see the `/meridian-work-coordination` skill.

## Parallel Spawns

Use `--bg` to launch spawns without blocking, then `wait` to collect results:

```bash
# Launch all spawns (each returns immediately with spawn ID)
meridian spawn -a coder -p "Implement auth" --bg --desc "auth"      # → p101
meridian spawn -a coder -p "Implement cache" --bg --desc "cache"    # → p102
meridian spawn -a reviewer -p "Review design" --bg --desc "review"  # → p103

# Single wait for all — one result, one summary
meridian spawn wait p101 p102 p103
```

**Why this pattern matters:** If you use your harness's background execution (e.g. Claude Code's `run_in_background: true`) on each spawn separately, you get N notifications as spawns complete one by one. Each notification triggers a response and summary, wasting tokens on partial progress. The `--bg` + single `wait` pattern gives you one notification when ALL spawns complete — one summary, no waste.

**Background wait discipline:** Treat backgrounded spawn ids as a pending set, not as "wait immediately after every spawn." Add each returned spawn id to the set. Keep launching the planned burst of `--bg` spawns, or do other local work that does not depend on their results. Drain the pending set with one combined `meridian spawn wait ...` at a barrier:

- before a final response
- before starting a next step that depends on those spawn results
- when local work is exhausted
- when the user explicitly asks for the outcome

When `meridian spawn wait ...` runs through a harness shell tool, do not poll constantly. Keep the same live shell session and poll it sparsely. Prefer long polling intervals such as 10 minutes (`yield_time_ms=600000`) between polls. Do not start a second `meridian spawn wait ...` while the first wait session is still active.

Wait has a 30-minute checkpoint. If spawns are still running, check their progress and re-wait:

```bash
# Check if spawn is active
meridian session log p101 --last 5

# Continue waiting if still making progress
meridian spawn wait p101 p102 p103
```

## Checking Status

Track spawns by their ID. For situational awareness, use the work dashboard — it shows active work items with their attached spawns:

```bash
meridian work
```

See `meridian-cli` for crash-only recovery behavior and reconciliation details.

To reattach to a spawn still running from a previous session, use `meridian spawn wait <spawn_id>`.

To see what a spawn spawned, use `spawn children`:

```bash
meridian spawn children p107   # list direct children of p107
```

`spawn show` also displays the parent ID when a spawn was created by another spawn.

## Steering a Running Spawn

**Inject before you cancel.** If a spawn is going in the wrong direction, send it additional context or a course correction — don't kill it and start over. Cancel destroys all the context the spawn has built up; inject preserves it.

```bash
meridian spawn inject p107 --message "Wrong approach — use the existing adapter pattern in src/adapters/"
```

## When a Spawn Fails

If a spawn returns `"status": "failed"`, read the report via `spawn show SPAWN_ID` — it usually contains the error or the agent's last output. For deeper investigation, see [`../meridian-cli/resources/debugging.md`](../meridian-cli/resources/debugging.md) for log inspection.

## Shared Filesystem

Spawns share filesystem directories for exchanging data without relying on conversation context (which does not survive across spawn boundaries). Query `meridian work current` for the work directory and `meridian context kb` for the knowledge base — these are not in environment variables.

See the `/meridian-work-coordination` skill for when to use which.

## Committing Spawn Changes

Use `spawn files` to stage exactly what a spawn changed — this avoids accidentally staging unrelated files that happened to be modified:

```bash
meridian spawn files p107 | xargs git add
meridian spawn files p107 -0 | xargs -0 git add   # paths with spaces
```

## Template Variables

Use `{{KEY}}` placeholders in prompts, replaced at launch time with `--prompt-var`. This keeps variable content visible in prompt logs and `--dry-run` output, where inline string interpolation would already be resolved:

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
