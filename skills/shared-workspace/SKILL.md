---
name: shared-workspace
description: Load when working in a shared repo with other agents or humans. Covers orientation, safety rules for uncommitted changes, and staging discipline.
disable-model-invocation: true
allow_implicit_invocation: false
---

# Shared Workspace

You are one of several agents working in this repository concurrently. Other agents — and humans — may have uncommitted changes, untracked files, or in-progress work that you cannot distinguish from stale artifacts by inspection alone.

## Orientation

At the start of your session:

1. Run `meridian context` to discover available context locations. `work` and
   `kb` always exist — others are project-specific. Route documentation and
   artifacts to the right context: work-scoped artifacts to `work`, durable
   knowledge to `kb`, project-specific contexts to their named location.
2. Run `meridian work` to see active work items and who else may be operating
   in the repo.
3. Check `git status` for uncommitted changes that may belong to another actor.

This prevents you from accidentally interfering with in-progress work and
ensures output lands in the right place.

## Safety Rules

- **Never revert, stash, or reset.** `git checkout .`, `git restore .`, `git reset --hard`, `git stash`, and `git clean` destroy other actors' uncommitted work. There is no safe version of these in a shared workspace. If you need a clean working tree, create a `git worktree` — it gives you an isolated checkout without touching the main tree.
- **Never delete untracked files without confirming ownership.** Untracked files may be another agent's or human's in-progress work. If a file looks unfamiliar, leave it alone.
- **Stage only files you changed.** Use specific paths with `git add`, not `git add -A` or `git add .`. When committing spawn output, use `meridian spawn files <id>` to get the exact file list your spawn modified, then stage only those.
- **Unfamiliar code is not evidence of error.** If you encounter code, patterns, or files you don't recognize, do not assume they are bugs or leftover artifacts. Confirm intent before modifying or removing them.
- **Escalate conflicts instead of resolving them.** If your work overlaps another actor's uncommitted edits, report the conflict to your caller rather than force-merging or overwriting.
