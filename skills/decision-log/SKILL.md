---
name: decision-log
description: >
  Decision capture methodology — reasoning, alternatives, and constraints.
  Use whenever you're making design choices, weighing alternatives,
  adapting plans, or rejecting an approach — any moment where "we chose X
  over Y" needs to survive beyond this conversation.
---

# Decision Log

Record decisions while the reasoning is fresh — in the moment you make the
choice, not retroactively. Reasoning flattens the longer you wait.

For each decision, capture: **what** was decided (concretely — name files,
interfaces, patterns affected), **why** it was chosen (constraints, evidence,
goals), and **what else** was considered (rejected alternatives and why they
lost). The rejected alternatives are often the most valuable part — they
prevent the next person from re-proposing what was already ruled out.

Record decisions where they naturally belong — in the relevant design doc,
plan annotation, architecture page, or KB entry. Not every decision needs
a dedicated file; what matters is that the reasoning is written down
somewhere durable.

Skip boilerplate decisions that follow directly from project conventions.
The test: would you want this context if you were reading this code for the
first time?
