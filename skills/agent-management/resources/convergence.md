# Convergence Patterns

## When to Stop

A loop converges when no new substantive findings emerge.

Substantive = would change the output, catch a real problem, or block
completion. Non-substantive = preferences, minor suggestions, things already
deferred.

## Review Loops

```
spawn reviewers → collect findings → route to fix → re-review affected areas
```

Stop when:
- No blocking findings remain
- New findings are all non-substantive
- Remaining items explicitly deferred with reasoning

## Fix Cycles

```
finding → scoped fix → verify fix → continue or re-review
```

Route findings to the smallest scope that can fix them. Re-run affected lanes
only, not the full loop on every iteration.

## Deferral

When something can't be resolved in this scope:
1. Log the deferral with reasoning
2. Note what would trigger revisiting
3. Move on

## Loop Guards

If a loop isn't converging after reasonable iterations:
- Check if findings are being addressed
- Check if new work is creating findings faster than fixes
- Consider whether scope is too large
- Escalate to caller if stuck
