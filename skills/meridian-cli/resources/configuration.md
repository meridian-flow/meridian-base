# Configuration

Meridian configuration controls defaults for models, agents, and timeouts.
Project config lives in `meridian.toml`; user config lives in
`~/.meridian/config.toml`. Resolved values can also come from env vars, agent
profile defaults, and CLI flags.

## Quick Reference

```bash
meridian config init              # scaffold config.toml with commented defaults
meridian config show              # show all resolved values with sources
meridian config get defaults.model  # get one key
meridian config set defaults.model gpt-5.4  # set one key
meridian config reset defaults.model  # remove override, revert to builtin
```

## Key Settings

### Models and Agents

| Key | Description | Default |
|-----|-------------|---------|
| `defaults.model` | Default model for `meridian spawn` when no `-m` given | *(empty — harness default applies)* |
| `defaults.harness` | Default harness routing target | `codex` |

`meridian spawn` without `-a` runs profile-less: no agent profile is loaded, the prompt runs against the resolved model with nothing more than the supplied skills and reference files. There is no implicit default agent. Pass `-a <profile>` to opt into a profile.

### Primary-Session Settings

Settings under the `primary.*` namespace apply to `meridian` primary sessions (the interactive top-level launch), not to `spawn` subagents.

| Key | Description | Default |
|-----|-------------|---------|
| `primary.model` | Model for primary sessions | *(null — inherits `defaults.model`)* |
| `primary.harness` | Harness for primary sessions | *(null — inherits `defaults.harness`)* |
| `primary.agent` | Agent profile for primary sessions | *(null — no profile)* |
| `primary.autocompact_pct` | Auto-compaction threshold (harness-specific) | *(null)* |

### Per-Harness Model Defaults

| Key | Description | Default |
|-----|-------------|---------|
| `harness.claude` | Default model routed to Claude | *(harness default)* |
| `harness.codex` | Default model routed to Codex | *(harness default)* |
| `harness.opencode` | Default model routed to OpenCode | *(harness default)* |

### Timeouts

| Key | Description | Default |
|-----|-------------|---------|
| `timeouts.wait_minutes` | How long `spawn wait` blocks before timing out | `120` |
| `timeouts.guardrail_minutes` | Grace period before timeout warning | `0.5` |
| `timeouts.kill_grace_minutes` | Grace period after SIGINT before SIGKILL | `0.033` |

### Retries

| Key | Description | Default |
|-----|-------------|---------|
| `defaults.max_retries` | Max retry attempts for failed spawns | `3` |
| `defaults.retry_backoff_seconds` | Backoff between retries | `0.25` |

## Config File Format

```toml
# meridian.toml

[defaults]
model = "gpt-5.4"
harness = "codex"

[primary]
agent = "my-orchestrator"

[timeouts]
wait_minutes = 60
```

## Resolution Order

Per-field precedence (later wins):

1. Builtin defaults (hardcoded)
2. User config (`~/.meridian/config.toml`)
3. Project config (`meridian.toml`)
4. Agent profile YAML
5. Environment variables (`MERIDIAN_*`)
6. CLI flags (`-m`, `-a`, `--timeout`, etc.)

Resolution is per field — a CLI override of one field must drive derived resolution for that field (e.g. `-m` overriding model also overrides harness routing), not inherit a profile's value indirectly.

Use `meridian config show` to see the resolved value and its source for every key.
