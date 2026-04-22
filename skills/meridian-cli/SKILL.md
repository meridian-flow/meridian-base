---
name: meridian-cli
description: "Mental model and principles for the meridian and mars CLIs. Use when an agent needs to discover what meridian can do, learn a subcommand, diagnose a failure, or understand why meridian behaves the way it does. Points at `meridian --help` and `meridian mars --help` as the canonical reference rather than duplicating them."
---

# meridian-cli

## 1. What Meridian Is

Meridian is a thin coordination layer for multi-agent systems. It is not a runtime, database, or workflow engine. It launches subagents through harness adapters, persists spawn and session state to disk, and exposes that state through one CLI.

State on disk is the source of truth. Use `meridian context` to find state locations.

## 2. CLI Surface by Command Group

| Command group | What it covers | Where to learn more |
|---|---|---|
| `meridian spawn` | Create, wait, list, show, log, cancel, stats, reports for subagent runs | `meridian spawn --help` |
| `meridian work` | Work item lifecycle, dashboard, session listing | `meridian work --help` |
| `meridian session` | Read and search harness session transcripts | `meridian session --help` |
| `meridian context` | Resolved filesystem context (work, kb, repo root, state root) | `meridian context --help` |
| `meridian models` | Model catalog and routing guidance | `meridian models list` |
| `meridian config` | Resolved config inspection and overrides | `meridian config --help` |
| `meridian doctor` | Health check and orphan reconciliation | `meridian doctor --help` |
| `meridian mars` | Bundled mars CLI for `.agents/` package management | `meridian mars --help` |

## 3. Principles `--help` Cannot Teach

**Output mode discipline.** Agent mode defaults to JSON while interactive terminals may default to text. Parse `spawn_id` and `status` from JSON responses, and avoid scraping prose from text output.

**Files as authority.** Spawn and session state lives under `.meridian/` as JSONL events plus per-spawn artifact directories. Never hand-edit `spawns.jsonl` or `sessions.jsonl`.

**Idempotent operations.** `meridian mars sync`, `meridian doctor`, and read-path reconcilers are built to converge when rerun. Re-running after interruption should reconcile state rather than duplicate side effects.

**Config precedence.** Resolution is per field: CLI flag, then `MERIDIAN_*` environment variable, then profile YAML, then project config, then user config, then harness defaults. A CLI model override must also drive derived harness resolution for that field. For concrete keys and defaults, see [`resources/configuration.md`](resources/configuration.md).

**Top-level chat id.** `$MERIDIAN_CHAT_ID` is the id of the top-level primary session at the root of the chat tree. Every spawn at any nesting depth inherits the same value, so session reads and searches default to the primary's transcript — where the root-level decisions and context live.

**Crash-only design.** Writes use atomic tmp+rename, reads tolerate truncation, and reconciliation happens on read paths. Recovery is startup behavior, not a shutdown hook.

**Spawn lifecycle.** Spawns transition `queued` → `running` → `finalizing` → `succeeded | failed | cancelled`. `finalizing` is a transient post-exit state where the runner is finishing report extraction and state persistence; treat it as active (not terminal) when polling. `spawn wait` blocks through `finalizing` and only returns on a terminal state. Reconciliation on read paths moves an abandoned `finalizing` record to `failed` with `orphan_finalization` when no recent activity is seen.

## 4. Diagnostics

`orphan_run` is common — runner died mid-flight. Check `meridian session log <spawn_id>` to see what happened, then relaunch. For other failure modes, see [`resources/debugging.md`](resources/debugging.md).

## 5. Sessions

`meridian session log <ref>` reads a transcript by chat id (`c123`), spawn id (`p123`), or raw harness session id. To read a session file directly, use `meridian session log --file path/to/session.jsonl`. Resolution falls back from active spawn output to the recorded harness transcript and then to stored spawn output — see `meridian session log --help` for the full resolution order per ref type. Segment `-c 0` reads the latest compacted segment; increment `-c` to walk older segments. `meridian session search <query> <ref>` searches case-insensitively across segments and returns navigation context.

`meridian work sessions <work_id>` lists sessions tied to a work item, and `--all` includes archived history. Combined with top-level chat id inheritance, this is the standard path to recover decision history for ongoing work.

For flag details, use `meridian session --help` and `meridian work sessions --help`.

## 6. Context Query

`meridian context` is the source of truth for filesystem context. It returns `work` and `kb` paths with their sources. Query it and use the literal paths — these are not projected into environment variables. See `meridian context --help` for flags.

- `meridian context work` — absolute path to the work directory
- `meridian context kb` — absolute path to the knowledge base directory
- `meridian context work.archive` — absolute path to the work archive
- `meridian work current` — absolute path to the work directory (same as `meridian context work` when a work item is active)

### Environment Variables

| Variable | Purpose |
|---|---|
| `MERIDIAN_STATE_ROOT` | Override `.meridian/` location |
| `MERIDIAN_DEPTH` | Spawn nesting depth (`>0` means inside a spawn) |
| `MERIDIAN_CHAT_ID` | Top-level primary session id (same value at every depth) |

## 7. Resources

- [`resources/debugging.md`](resources/debugging.md) — failure modes, artifact layout, log inspection
- [`resources/configuration.md`](resources/configuration.md) — config keys, precedence, defaults
- [`resources/mars-toml-reference.md`](resources/mars-toml-reference.md) — full `mars.toml` schema

## 8. Where to Go Next

- For delegation patterns and model routing, load `meridian-spawn`.
- For work lifecycle and artifact placement, load `meridian-work-coordination`.
- For capability-limit escalation flow, load `meridian-privilege-escalation`.
