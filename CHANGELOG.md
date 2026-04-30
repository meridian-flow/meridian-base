# Changelog

Be brief. Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Versioning: [SemVer](https://semver.org/). Versions before 0.0.13 in git history only.

## [Unreleased]

### Changed
- `meridian-cli`, `meridian-spawn`, `meridian-work-coordination`: context dirs now available as `MERIDIAN_CONTEXT_*_DIR` env vars, injected into agent system prompts at launch. Removed "not in environment variables" guidance. `$MERIDIAN_WORK_DIR` documented as active work item (separate from `$MERIDIAN_CONTEXT_WORK_DIR` work root).
- `meridian-spawn`: trimmed 1589→756w. Absorbed meridian-cli §3 (output discipline, spawn lifecycle, crash-only design). Fixed abbreviated commands. Removed redundant examples.
- `decision-log`: trimmed 412→186w. Core ideas only — record while fresh, what/why/what-else, skip obvious. Decisions belong where they naturally belong, not a mandatory file path.
- `md-validation`: trimmed body, moved mermaid authoring and command reference to resources/.
- `meridian-privilege-escalation`: removed stale `unrestricted` sandbox tier and `--add-dir` flag.
- `kb-writer`: removed mandatory `decisions.md` file references. Decision mining captures to relevant domain docs.
- `kb-maintainer`: removed `decisions.md` verification requirements.
- `kb-conventions`: removed `decisions.md` from KB layout.
- All skills get explicit invocation-control flags. Safety nets (`meridian-privilege-escalation`) explicitly flipped to allow implicit loading.
- `AGENTS.md`: fixed title (was copy of dev-workflow), added `mars version` release guidance.
- `README.md`: removed stale `meridian-cli` skill from table, fixed parallel spawn example, removed stale `meridian report search`.

### Removed
- `agent-creator` skill (entire directory with resources/).
- `skill-creator` skill (entire directory with resources/).
- `meridian-cli` from `explorer`, `kb-writer`, `kb-maintainer`, `meridian-default-orchestrator` skill lists.

## [0.0.36] - 2026-04-30

### Changed

- update kb-maintainer and md-validation

## [0.0.34] - 2026-04-30

### Changed
- `@kb-maintainer`: target-aware path resolution — operates on an explicitly passed tree (e.g., work-item `design/` via `-f`) or defaults to the durable KB via `meridian context kb`. KB-specific commands (`meridian kg check/graph`) gated to KB target only; non-KB targets use `rg` and directory listing for cross-reference analysis. Description, body, and workflow all generalized. Navigability and diagram validation sections apply to any target with the relevant files present.
- `md-validation` skill: documents shipped `mermaid check` style warnings — `--strict`, `--no-style`, `--disable` flags; default categories (`ox-edge`, `bare-end`, `fill-no-color`); inline suppression syntax (`%% mermaid-check-ignore[-next-line] [category]`); warning output format and exit code semantics. Authoring rules and dark/light mode color guidance preserved.

## [0.0.33] - 2026-04-28

### Added
- `@explorer` agent: moved from meridian-dev-workflow. Generic cheap reader — codebase, sessions, work items. Nothing dev-workflow-specific about it.

### Changed
- All base agents now use direct model names instead of project-specific aliases. Aliases like `gpt`, `sonnet`, `codex`, `gptmini` only exist in the consuming project's config — base agents must resolve anywhere.
- `@kb-maintainer`: add `meridian-spawn` skill so it can delegate bulk reading.
- `shared-workspace` skill: orientation now starts with `meridian context` to discover available context locations (work, kb, project-specific). Route artifacts to the right context instead of hardcoding paths.
- `shared-workspace` skill: revert/stash/reset rule tightened — no escape hatch. Use `git worktree` if you need a clean tree.

## [0.0.32] - 2026-04-28

### Added
- `@kb-writer` agent: writes and updates KB — decisions, domain knowledge, architecture, synthesized research. Replaces content-writing half of old `@code-documenter`. Sonnet model, workspace-write.
- `@kb-maintainer` agent: structural health of KB — splits oversized docs, merges fragments, fixes cross-references, flags contradictions with `> [!FLAG]` markers for human review. GPT model, workspace-write.
- `kb-conventions` skill: shared KB structure, navigation, writing standards, flag protocol, what-belongs-where table. Loaded by both kb-writer and kb-maintainer.
- `decision-log` skill: moved from meridian-dev-workflow. Decision capture methodology — reasoning, alternatives, constraints.
- `session-mining` skill: moved from meridian-dev-workflow. Transcript mining patterns — top-level session recovery, bulk delegation to explorer, per-work-item session discovery.

### Changed
- Model catalog guidance: use `meridian mars models list`, not old `meridian models list`.

## [0.0.30] - 2026-04-25

### Changed
- `meridian-spawn` skill: background waits now taught as pending-set barriers, not per-spawn immediate waits. Explicitly says do not poll constantly, prefer sparse 10-minute shell polling, and keep one live wait session instead of reissuing waits.

## [0.0.29] - 2026-04-24

### Added
- `meridian-spawn` skill: "Steering a Running Spawn" section — teaches inject-before-cancel pattern so agents send course corrections instead of killing spawns.

### Changed
- `meridian-default-orchestrator`, `meridian-subagent`: broaden disallowed git commands — block `checkout`, `switch`, `stash` (not just `checkout --`).

## [0.0.28] - 2026-04-24

### Added
- `md-validation` skill — teaches agents `meridian kg` (link topology, broken link checking) and `meridian mermaid check` (diagram validation). Loaded by doc-facing agents.

## [0.0.26] - 2026-04-21

### Added
- `AGENTS.md` and `CLAUDE.md` — project instructions from dev-workflow.

### Changed
- `meridian-cli` skill: `$MERIDIAN_CHAT_ID` semantics corrected — top-level primary session id at root of chat tree, not parent. Fixed in prose, env-var table, sessions section. `session log <ref>` expanded: covers chat id / spawn id / harness session id; `--file path/to/session.jsonl` documented as separate invocation form; fallback resolution noted.
- `meridian-spawn` skill: new `--from` section. Covers `<spawn-id>` for predecessor reasoning and `$MERIDIAN_CHAT_ID` for top-level primary conversation. Examples show env-var paired with `-f` files (framing vs scope discipline).
- Context backend migration: `work_dir`/`fs_dir` → `work`/`kb`. All skills now use `meridian work current` and `meridian context kb` instead of old field names or env vars.
- `meridian-cli/SKILL.md`: context query section rewritten — documents `meridian context work`, `meridian context kb`, `meridian context work.archive`. Removed stale `work_dir`/`fs_dir`/`repo_root`/`state_root`/`depth`/`context_roots` field list.
- `meridian-spawn/SKILL.md`: shared filesystem section uses new query commands. `-f` now documented as accepting folders (tree listing) plus files (full content) — folder-for-map, file-for-content pattern.
- `meridian-work-coordination/SKILL.md`: artifact placement uses `work`/`kb` vocabulary with query commands.
- `meridian-cli/resources/debugging.md`: removed `$MERIDIAN_FS_DIR` env var refs — replaced with `meridian context kb` query.
- `agent-creator`: removed `$MERIDIAN_WORK_DIR` env var refs from SKILL.md, example-profiles.md, anti-patterns.md.
- `meridian-prompter` dep switched to `local-dependencies` — expects sibling checkout.
- `meridian-spawn/SKILL.md`: teach `--bg` + single `wait` pattern for parallel spawns. Old pattern (harness-level `run_in_background` per spawn) caused N notifications and N partial summaries — token waste. New pattern: launch with `--bg`, single `wait` for all, one summary. Documents 30-minute wait checkpoint.

## [0.0.21] - 2026-04-18

### Changed
- Skills trimmed for conciseness — context query model uses `meridian context` / `meridian work current` instead of env vars.
- `meridian-cli`: diagnostics tables moved to `resources/debugging.md`, Mars section removed (use `--help`), Resources section added, Context Query section trimmed.
- `meridian-spawn`: inject docs trimmed (1 line), prompt examples simplified.
- `meridian-work-coordination`: artifact placement updated for context query pattern.


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
