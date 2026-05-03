---
name: meridian-work-coordination
description: Use whenever you need to create, switch, update, or complete a work item, or decide where work-scoped notes versus broader shared docs belong.
model-invocable: false
---

# Work Coordination

The orchestrator owns work state — subagents should not mutate it unless explicitly instructed, because concurrent mutations from multiple spawns create race conditions and inconsistent status.

If meaningful repo work is about to start, create or attach to a work item:

```bash
meridian work start "descriptive name"   # create new
meridian work switch descriptive-name    # attach to existing
```

## Dashboard

```bash
meridian work                    # dashboard — what's in flight
meridian work list               # list active work items
meridian work list --done        # list done/archived items
meridian work show auth-refactor # drill into one work item
```

## Status Management

Status values are free-form. Keep the current phase visible:

```bash
meridian work update auth-refactor --status designing
meridian work update auth-refactor --status implementing
meridian work done auth-refactor
meridian work reopen auth-refactor
meridian work delete stale-item          # remove empty work items
meridian work delete old-item --force    # remove even if it has artifacts
```

`work done` archives the work directory. `work reopen` restores it. `work delete` removes the work item entirely — requires `--force` if it has artifacts.

## Artifact Placement

**work root** (`$MERIDIAN_CONTEXT_WORK_DIR`) — container holding all work item subdirectories. Do NOT write artifacts here directly.

**active work** (`$MERIDIAN_ACTIVE_WORK_DIR`) — active work item subdirectory. Write work-scoped artifacts here. If unset, no work item is active; create one with `meridian work start`.

**kb** (`$MERIDIAN_CONTEXT_KB_DIR`) — long-lived reference material. Persists across work items.

Rule of thumb: *this* work item → work dir. Project-wide → kb.

## Commit Work Artifacts

Sessions can crash or compact. Work artifacts are the only thing that survives — commit them frequently, not just at the end.
