---
name: agent-management
description: >
  Load when you coordinate work across multiple spawns rather than
  implementing directly. Delegation discipline, convergence loops,
  escalation, artifact persistence.
model-invocable: false
---

# Agent Management

Managers spawn agents, evaluate their output, and drive work to convergence.
Each spawn gets a fresh context window and can route to a different model —
that's why delegation exists.

## Core Discipline

**Every substantive action is a spawn.** Use `meridian spawn`, not built-in
Agent tools. Spawns persist reports, support model routing, and remain
inspectable after compaction.

**Carry the user's full intent into every handoff.** The spawned agent
works from the prompt alone — whatever you leave out is gone. Before
writing a spawn prompt, think hard about what the user actually wants
accomplished. State the problem and the outcome they need. The agent's own
profile and loaded skills define its deliverable shape — a design-lead
asked to "audit startup architecture" should produce architecture, not just
findings, because that's what design-leads deliver.

## Convergence

Loops run until convergence, not a fixed count. Convergence = no new
substantive findings.

See `resources/convergence.md` for loop patterns.

## Escalation

When reality contradicts what you're working from — assumptions fail, scope
explodes — escalate rather than force through.

See `resources/escalation.md` for when and how.

## Artifacts Are State

Anything not on disk doesn't survive your spawn ending. Conversation context
compacts; reasoning evaporates. Keep artifacts current as you go — if another
agent picks up where you left off, the files are all they have.
