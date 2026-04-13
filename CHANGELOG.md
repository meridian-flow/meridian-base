# Changelog

Caveman style. Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Versioning: [SemVer](https://semver.org/). Versions before 0.0.13 in git history only.

## [Unreleased]

### Changed
- `agent-creator` and `skill-creator`: "dial back aggressive language" guidance now more precise. Prefer ordinary framing by default, but keep load-bearing negatives when they carry a real boundary with reasoning. Warn against blind rewrite sweeps that strip every `must`/`never`.

## [0.0.14] - 2026-04-10

### Fixed
- `meridian-cli/resources/mars-toml-reference.md`: "Item Filtering" section claimed `agents`/`skills` and `exclude` could combine ("Both: `agents`/`skills` defines the set, `exclude` removes from it"). Wrong — per mars source, `FilterConfig::to_mode()` makes filter modes mutually exclusive. When both are set, `exclude` is silently dropped. Rewrote the section to document the actual precedence order and the mutual-exclusion rule. Sent an agent down a rabbit hole this session (see `meridian-dev-workflow/CHANGELOG.md` `0.0.16` entry).

## [0.0.13] - 2026-04-10

### Added
- `CHANGELOG.md`. Keep a Changelog format, caveman style.

### Changed
- Git URLs moved `haowjy/` → `meridian-flow/` org.
