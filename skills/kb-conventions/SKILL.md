---
name: kb-conventions
description: >
  Conventions for the meridian knowledge base — structure, navigation,
  writing standards, and the flag protocol for human review. Load when
  reading from, writing to, or maintaining the KB. Also load when deciding
  what belongs in the KB vs the work directory.
disable-model-invocation: true
allow_implicit_invocation: false
---

# KB Conventions

The KB (`meridian context kb`) is a persistent, compounding knowledge base —
a wiki that accumulates understanding across work sessions. Agents and humans
read it to orient. Its value grows with every work item if content is
well-structured and maintained.

## Structure

```
kb/
  index.md              # page catalog — links + one-line summaries by domain
  <domain>/             # topic directories, emerge organically
    overview.md         # orients on the domain, links to pages
    <topic>.md          # one coherent concept per doc
```

- `index.md` is the entry point. Read it first to understand what's documented.
- Domains emerge as the KB grows. A domain earns a directory when it has
  enough distinct concepts for multiple docs.

## Navigation

1. Start with `index.md` to find relevant pages
2. Read domain overviews to understand a topic area
3. Drill into specific pages for detail
4. Use `meridian kg graph` to see link topology and find related pages
5. Search for `> [!FLAG]` to find items needing human attention

## Writing Standards

**Diagram first.** A picture is worth a thousand words. Diagrams communicate
structure, relationships, and flows faster and more concretely than prose.
Default to mermaid diagrams for anything spatial — module boundaries, data
flows, state machines, dependency graphs, sequence interactions, system
topology. Prose supplements diagrams, not the other way around. Tree structures
work well for hierarchical decomposition. Validate with `meridian mermaid check`.

**Single responsibility.** Each doc covers one coherent concept. Two unrelated
topics in one doc → split. Two docs on the same concept → merge or
cross-reference.

**Write for humans and agents.** If nobody browses it, nobody maintains it.
Clear prose with structure beats compressed bullet-point dumps.

**Explain the why.** Code shows what. The KB explains why — reasoning,
constraints, tradeoffs, rejected alternatives.

**Cross-reference, don't re-explain.** Link to other docs instead of
duplicating their content. Keeps each doc focused and prevents drift.

## What Belongs Where

| Content | Location | Rationale |
|---|---|---|
| Durable decisions and rationale | KB | Persists across work items |
| Domain knowledge, patterns, quirks | KB | Compounds over time |
| Architecture and design rationale | KB | Agents orient from it |
| Synthesized research findings | KB | Raw research → work dir; lasting insights → KB |
| Retrospective learnings | KB | Prevents repeating mistakes |
| Active work artifacts | Work dir | Scoped to current work item |
| Raw research, exploration | Work dir | Messy and ephemeral |
| In-progress design/plans | Work dir | Moves to KB when durable |

## Flag Protocol

When content needs human attention — contradictions between sources, ambiguous
intent, claims that can't be verified, policy questions — flag it inline:

```markdown
> [!FLAG] **Needs human review** — Brief description of the issue.
> Flagged YYYY-MM-DD.
```

Flags are visible when browsing and searchable with `rg '\[!FLAG\]'`. Resolve
flags by removing the blockquote after the issue is addressed.

## Validation

- `meridian kg check` — broken cross-references (run before committing KB changes)
- `meridian kg graph` — link topology, orphan pages, hub pages
- `meridian mermaid check` — diagram validity
