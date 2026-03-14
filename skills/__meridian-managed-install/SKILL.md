---
name: __meridian-managed-install
description: Managed install system for syncing agent profiles and skills from external sources (git repos, local paths) into .agents/. Use this whenever you need to install, update, uninstall, or list managed sources of agents or skills, check install drift with `meridian status`, understand the agents.toml manifest or agents.lock file, troubleshoot missing agents/skills, set up a freshly cloned repo, or share agents across projects. Also use this when the user mentions syncing, installing, or managing agent sources — even if they don't say "install" explicitly.
---

# Managed Install

Meridian's managed install system keeps `.agents/` in sync with external sources — git repos or local paths. Instead of manually copying agent profiles and skills into `.agents/`, declare where they come from and let the install engine handle reconciliation, conflict detection, and provenance tracking.

This matters because `.agents/` is the single discovery root for all agent profiles and skills. If content isn't there, agents can't find it. The install system is how content gets there reproducibly.

## Mental Model

Think of it like a package manager for agents and skills:

- **`agents.toml`** is your manifest — what sources you want, what items from each
- **`agents.lock`** is the lock file — exact resolved versions for reproducibility
- **`.agents/`** is the install target — where content lands on disk

The engine compares source ↔ lock ↔ local to decide what to install, update, skip, or flag as conflicting. Local modifications are preserved by default (never silently overwritten).

## Addressing

Three explicit forms for specifying sources:

- **Local path**: `./path` or `~/path` or absolute path
- **GitHub shorthand**: `@owner/repo` (expands to `https://github.com/owner/repo.git`)
- **Full URL**: `https://github.com/org/repo.git`

## Commands

### Install a source

```bash
meridian install <source> [--name NAME] [--ref REF] [--agents a1,a2] [--skills s1,s2]
```

The CLI auto-detects source type:

```bash
meridian install ./local-agents --name local            # Local path
meridian install @haowjy/meridian-core                  # GitHub shorthand
meridian install https://github.com/org/repo.git --ref main  # Full URL
```

Install is **additive** — running it against an existing source merges new agents/skills into the existing selection instead of erroring:

```bash
meridian install @haowjy/meridian-core --agents __meridian-orchestrator
meridian install @haowjy/meridian-core --agents __meridian-subagent  # adds, doesn't error
```

Without `--agents` or `--skills`, everything in the source is installed. Use filters when you only need specific items — this avoids polluting `.agents/` with content you don't want.

**Skill dependency resolution**: When installing agents with `--agents`, the engine automatically resolves skill dependencies from each agent's frontmatter and installs them too. No need to manually specify `--skills` for deps.

```bash
meridian install @haowjy/meridian-core --agents __meridian-orchestrator
# → also installs __meridian-orchestrate, __meridian-spawn-agent (skill deps)
```

Rename items at install time with `--rename agent:old-name=new-name` to avoid collisions.

### Sync from lock (bare install)

```bash
meridian install
```

With no source argument, syncs from the locked state. Use this after cloning a repo (the lock file is committed, `.agents/` content might not be) or to reset managed files back to their locked state.

### Update (re-resolve and install)

```bash
meridian update [--source NAME]
```

Re-resolves floating refs (like `main`) to the latest commit, then installs. This is how you pull upstream improvements. Pinned refs (tags, commit SHAs) won't change — only branches advance. Running it twice produces identical results.

### Check for drift (status)

```bash
meridian status
```

Compares lock file against on-disk content. Reports:

| Status | Meaning | Action |
|--------|---------|--------|
| `in-sync` | Local matches lock | Nothing needed |
| `locally-modified` | You edited a managed file | Decide: keep edits or `update --force` to reset |
| `missing` | Lock says it should exist, but file is gone | `meridian install` to restore |
| `orphaned` | File exists but source is gone from lock | Remove manually or ignore |

### List installed items

```bash
meridian list
```

Shows installed items grouped by source — agents and skills from each source.

### Uninstall items or sources

```bash
meridian uninstall <item-name> [<item-name> ...]     # Remove specific items
meridian uninstall --source <source-name> [--force]  # Remove entire source
```

Item-level uninstall removes items from `.agents/` and from the source's filter in `agents.toml`. If the last item is removed from a source, the source is auto-cleaned up. Source-level uninstall removes the entire source and all its managed files.

Without `--force`, locally modified files are kept — the engine won't silently destroy your edits.

### Common flags

All install commands accept:
- `--dry-run` — preview what would happen without writing anything
- `--force` — overwrite local modifications (use deliberately, not by default)

## Choosing the Right Command

| Situation | Command |
|-----------|---------|
| Fresh clone, need agents/skills | `meridian install` |
| Adding a new external source | `meridian install <source>` |
| Adding items to an existing source | `meridian install <source> --agents name1,name2` |
| Pulling latest from upstream | `meridian update` |
| Something seems wrong or drifted | `meridian status` |
| See what's installed | `meridian list` |
| Removing specific items | `meridian uninstall <item-name>` |
| Removing a source you no longer need | `meridian uninstall --source <name>` |
| Previewing before committing | Add `--dry-run` to any command |

## State Files

| File | Role | Edit? |
|------|------|-------|
| `.meridian/agents.toml` | Source manifest | Yes — this is your config |
| `.meridian/agents.lock` | Resolved state | No — generated by the engine |
| `.meridian/cache/` | Git clone caches | No — managed automatically |

The manifest is where you declare intent. The lock file records what was actually installed. Both should be committed to version control — the lock enables deterministic `install` on other machines.

Read [`resources/manifest-reference.md`](resources/manifest-reference.md) for the full `agents.toml` schema, item filtering, rename format, and source layout conventions.

Read [`resources/internals.md`](resources/internals.md) for lock file structure, conflict resolution logic, content hashing, bootstrap behavior, and caching.
