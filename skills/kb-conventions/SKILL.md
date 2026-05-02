---
name: kb-conventions
description: >
  Load when reading from, writing to, or maintaining the KB. Covers
  structure, layer conventions, and operational tooling.
disable-model-invocation: true
allow_implicit_invocation: false
---

# KB Conventions

Load `/llm-writing` if it isn't already loaded.

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

## Wiki Structure

`index.md` is the entry point — a catalog with links and summaries.
Directories emerge organically; nest as deep as the content warrants. One
concept per doc — split when topics diverge, merge when they overlap. Link
to other docs instead of re-explaining their content. Use mermaid diagrams
for anything spatial; validate with `meridian mermaid check`.

Wiki pages teach how things work — the mechanism, the flows, the edge cases.
When a fact results from a decision, a brief reference links to the decision
record. Readers can understand the system from wiki pages alone, and follow
links to decisions when they need the reasoning.

## Operations

Flag content needing human attention with `> [!FLAG] **Needs human review**`.
Searchable with `rg '\[!FLAG\]'`.

Before committing: `meridian kg check` (broken links), `meridian mermaid check`
(diagram validity). `meridian kg graph` shows link topology.
