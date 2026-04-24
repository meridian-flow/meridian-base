---
name: md-validation
description: Use when writing, reviewing, or refactoring markdown docs and you need fast validation of links or Mermaid diagrams. Trigger on phrases like "check docs", "find broken links", "validate mermaid", "map doc structure", or before committing documentation changes.
---

# md-validation

## 1. What This Skill Covers

Use Meridian's markdown validation commands:

- `meridian kg` for link analysis and document topology
- `meridian mermaid check` for Mermaid diagram validation

These tools work on any markdown file or directory target, not just Meridian knowledge-base content.

## 2. `meridian kg` Quick Stats

Run `meridian kg [path]` for a fast status pass.

It reports:

- file count
- link count
- broken link count

Use this as an immediate sanity check while editing docs.

## 3. `meridian kg graph` Link Topology

Run `meridian kg graph [path]` to see which documents link to which.

Targets:

- accepts a file path
- accepts a directory path

Common flags:

- `--depth N` link-hop depth (default `3`)
- `--external` include external URLs as leaf nodes
- `--exclude PATTERN` exclude matches by glob (repeatable)
- `--format json` machine-readable output

Persistent exclusions:

- add patterns to `.kgignore` at the scan root
- format is gitignore-style matching via `pathspec`

Tree markers to interpret:

- `(already shown)` node was previously rendered
- `(N links hidden)` links were truncated by depth/filters
- `(not found)` local target could not be resolved

Use this when joining a project or exploring unfamiliar docs before making structural changes.

## 4. `meridian kg check` Broken-Link Gate

Run `meridian kg check [path]` as a commit/CI gate for markdown links.

Flags:

- `--exclude PATTERN` exclude matches by glob (repeatable)
- `--format json` machine-readable output

Exit behavior:

- `0` no broken links
- `1` broken links found

Run before committing doc or KB updates.

## 5. `meridian mermaid check` Diagram Validation

Run `meridian mermaid check [path]` to validate Mermaid content in:

- fenced Mermaid blocks inside markdown files
- standalone `.mmd` files
- standalone `.mermaid` files

Flags:

- `--depth N` directory traversal depth
- `--exclude PATTERN` exclude matches by glob (repeatable)
- `--format json` machine-readable output

Persistent exclusions:

- `.mermaidignore` at the scan root

Parser modes:

- Python heuristic parser by default
- optional JS strict parser when Node.js is available

Catches common failures:

- unknown diagram types
- unclosed directives
- mismatched Mermaid block boundaries

Exit behavior:

- `0` clean
- `1` validation errors
- `2` target path not found

Run this after diagram edits and during review of docs containing Mermaid.

## 6. When To Use Which Command

- Use `meridian kg check` before committing documentation changes to catch broken links.
- Use `meridian kg graph` to understand doc structure and link topology.
- Use `meridian mermaid check` when writing or reviewing docs with diagrams.

## 7. Minimal Validation Flow

1. `meridian kg [path]`
2. `meridian kg check [path]`
3. `meridian mermaid check [path]`
4. Fix issues and re-run until exit codes are clean

## 8. Reference

Use `meridian kg --help` and `meridian mermaid --help` for full option coverage.
