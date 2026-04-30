---
name: md-validation
description: >
  Use when writing, reviewing, or refactoring markdown docs and you need
  fast validation of links or Mermaid diagrams. Trigger on phrases like
  "check docs", "find broken links", "validate mermaid", "map doc
  structure", or before committing documentation changes.
disable-model-invocation: true
allow_implicit_invocation: false
---

# md-validation

Meridian's markdown validation commands work on any markdown file or directory,
not just KB content.

## Commands

- **`meridian kg check [path]`** — broken-link gate. Exit `0` clean, `1` broken
  links. Run before committing doc changes.
- **`meridian kg graph [path]`** — link topology visualization. Use to understand
  doc structure and find orphan pages.
- **`meridian kg [path]`** — quick stats (file count, link count, broken count).
- **`meridian mermaid check [path]`** — Mermaid diagram validation (syntax +
  style warnings). Run after diagram edits.

## Validation Flow

1. `meridian kg [path]` — quick sanity check
2. `meridian kg check [path]` — catch broken links
3. `meridian mermaid check [path]` — validate diagrams
4. Fix issues and re-run until exit codes are clean

Use `meridian kg --help` and `meridian mermaid --help` for full flag coverage.

See `resources/mermaid-authoring.md` for diagram authoring rules (quoting,
line breaks, reserved words, themes).
See `resources/command-reference.md` for detailed flags and warning categories.
