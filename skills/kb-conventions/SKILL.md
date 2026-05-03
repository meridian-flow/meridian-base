---
name: kb-conventions
description: >
  Load when reading from, writing to, or maintaining the KB. Covers layer
  model, structural conventions, and operational tooling.
model-invocable: false
---

# KB Conventions

Load `/llm-writing` if it isn't already loaded.

## Five Layers

The KB (`meridian context kb`) has five layers:

**Sources** — raw material: articles, research, transcripts, data. Immutable.
The LLM reads from sources but never modifies them.

**Decisions** — the reasoning layer. Why a source matters, why the wiki is
shaped the way it is, what was chosen and rejected. Decisions connect sources
to knowledge by explaining the interpretation and judgment applied.

**Wiki** — synthesized knowledge pages. How things work, concepts,
architecture. The LLM owns this layer: creates pages, updates them, maintains
cross-references, keeps everything consistent. Shaped by the decisions.

**Log** — what changed in the wiki and why. The audit trail of how the KB
evolved — what was added, updated, or restructured, and what triggered it.

**Schema** (`AGENTS.md`) — user-defined governance. Tells the LLM how the
layers are structured, what conventions to follow, and what workflows to use.
The human and LLM co-evolve this. `CLAUDE.md` references `AGENTS.md` via
`@AGENTS.md`.

## Wiki Page Conventions

### One Concept Per Document

Each doc covers one coherent concept — one component, one interaction pattern,
one decision. When a doc covers two unrelated topics, split it. When two docs
explain the same concept from different angles, merge or cross-reference.

Name files by what they describe (`token-validation.md`), not when they were
written (`auth-redesign-notes.md`).

### Hierarchical Organization

`index.md` is the entry point — a catalog with links and one-line summaries,
organized by domain. Directories emerge organically as a domain accumulates
enough distinct concepts. Nest as deep as the content warrants.

```
index.md
<domain>/
  overview.md
  <topic>.md
  <sub-domain>/
    <topic>.md
```

The overview at each level orients the reader: what exists here, how pieces
relate, where to go deeper.

### Linking

Link to related docs using relative paths. Cross-reference instead of
re-explaining — each doc stays focused and there's one source of truth per
concept. When a fact results from a decision, link to the decision record.
Readers understand the system from wiki pages alone, following links to
decisions when they need the reasoning.

### Readability

Write docs that work in isolation:

- **Self-contained** — enough inline context that a reader doesn't need three
  other docs first
- **Scannable** — headers, bullet lists, tables. Bold key terms on first use.
- **Concrete** — file paths, function names, type signatures over vague
  references
- **State invariants explicitly** — agents follow what's written

### Style

Wiki pages teach how things work — mechanism, flows, edge cases. Capture what
code can't easily tell you: component relationships, dependency directions,
data flows, and why the system is shaped this way.

Use mermaid diagrams for anything spatial — component relationships, data
flows, state machines. Tables for comparisons. Reference source locations
rather than pasting code.

## Operations

Flag content needing human attention with `> [!FLAG] **Needs human review**`.
Searchable with `rg '\[!FLAG\]'`.

Before committing: `meridian kg check` (broken links), `meridian mermaid check`
(diagram validity). `meridian kg graph` shows link topology.
