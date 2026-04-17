# Changelog

Caveman style. Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Versioning: [SemVer](https://semver.org/). Versions before 0.0.13 in git history only.

## [Unreleased]

## [0.0.19] - 2026-04-17

### Added
- `shared-workspace` skill: moved from `meridian-dev-workflow`. Multi-agent safety for shared repos — orientation on active work, rules for not destroying other actors' uncommitted changes, staging discipline. Base agents and dev-workflow agents both need it.

## [0.0.18] - 2026-04-17

### Changed
- `@meridian-default-orchestrator` and `@meridian-subagent`: comprehensive `disallowed-tools` hardening. Block `ScheduleWakeup`, `Cron*`, `PushNotification`, `RemoteTrigger`, `EnterPlanMode`, `ExitPlanMode`, `EnterWorktree`, `ExitWorktree`, `NotebookEdit`. Keep `LSP` and `Monitor` available. Orchestrator keeps `Task*` and `AskUserQuestion`.

## [0.0.17] - 2026-04-16

### Changed
- `meridian-cli/SKILL.md`: add `finalizing` to spawn lifecycle ("queued → running → finalizing → terminal"). Teach `finalizing` as transient/non-terminal so agents stop treating it as stuck. Failure-mode table: drop `orphan_stale_harness` (deleted upstream), add `orphan_finalization` row, rename `missing_wrapper_pid`/`missing_worker_pid` → `missing_runner_pid`.
- `meridian-cli/resources/configuration.md`: drop `defaults.agent` and `defaults.primary_agent` rows (both removed upstream). Teach profile-less spawn default when no `-a`. Add `primary.*` namespace section for primary-session knobs. Add `defaults.harness` row. Bump `timeouts.wait_minutes` default 30 → 120. Tighten resolution-order list to match live precedence; add per-field-override note.
- `meridian-cli/resources/debugging.md`: teach that `finalizing` is not stuck, give the reconciliation path to `orphan_finalization`, note `report.md` may still hold useful content after abandonment.
- `meridian-spawn/resources/advanced-commands.md`: remove `meridian spawn report create` examples (subcommand deleted upstream). Fix `report show` example to positional form. Add explanatory note that reports are written by the spawned agent itself at end-of-run.
- `meridian-spawn/SKILL.md`: Parallel Spawns section names the concrete Claude Code mechanism — `run_in_background: true` Bash tool parameter. Prior text said "use your harness's native background execution" abstractly; example-less direction didn't map cleanly to the specific tool agents actually have. Concrete syntax for Claude Code; pointer to "equivalent mechanisms" for other harnesses.
- `agent-creator` and `skill-creator`: "dial back aggressive language" guidance now more precise. Prefer ordinary framing by default, but keep load-bearing negatives when they carry a real boundary with reasoning. Warn against blind rewrite sweeps that strip every `must`/`never`.

## [0.0.14] - 2026-04-10

### Fixed
- `meridian-cli/resources/mars-toml-reference.md`: "Item Filtering" section claimed `agents`/`skills` and `exclude` could combine ("Both: `agents`/`skills` defines the set, `exclude` removes from it"). Wrong — per mars source, `FilterConfig::to_mode()` makes filter modes mutually exclusive. When both are set, `exclude` is silently dropped. Rewrote the section to document the actual precedence order and the mutual-exclusion rule. Sent an agent down a rabbit hole this session (see `meridian-dev-workflow/CHANGELOG.md` `0.0.16` entry).

## [0.0.13] - 2026-04-10

### Added
- `CHANGELOG.md`. Keep a Changelog format, caveman style.

### Changed
- Git URLs moved `haowjy/` → `meridian-flow/` org.
