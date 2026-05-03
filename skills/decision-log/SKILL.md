---
name: decision-log
description: >
  Use whenever you're making design choices, weighing alternatives,
  adapting plans, or rejecting an approach — any moment where "we chose X
  over Y" needs to survive beyond this conversation.
model-invocable: false
---

# Decision Log

Load `/intent-modeling` if it isn't already loaded.

Record decisions while the reasoning is fresh. Reasoning flattens the longer
you wait — capture it in the moment, not retroactively.

A decision record makes the LLM's understanding visible: what the human
said, what you interpreted their intent to be, what goal you derived from
that, and what you chose based on that understanding. This is a transparency
layer — if the interpretation is wrong, the record makes it checkable and
correctable before the misreading gets baked into code or artifacts.

The rejected alternatives are often the most valuable part. They prevent
the next person from re-proposing what was already considered.

Skip boilerplate decisions that follow directly from project conventions.
The test: would you want this context if you were reading this code for the
first time?
