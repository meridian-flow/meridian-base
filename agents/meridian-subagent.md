---
name: meridian-subagent
description: >
  Minimal default subagent profile for repo-local Meridian work. Spawn with
  `meridian spawn -a meridian-subagent`, passing task files with -f.
  Executes the described task directly and reports results.
model: gpt-5.3-codex
skills: [shared-workspace]
tools: [Bash, Write, Edit]
disallowed-tools: [Agent, NotebookEdit, ScheduleWakeup, CronCreate, CronDelete, CronList, TaskCreate, TaskGet, TaskList, TaskOutput, TaskStop, TaskUpdate, AskUserQuestion, PushNotification, RemoteTrigger, EnterPlanMode, ExitPlanMode, EnterWorktree, ExitWorktree, Bash(git revert:*), Bash(git checkout:*), Bash(git switch:*), Bash(git stash:*), Bash(git restore:*), Bash(git reset --hard:*), Bash(git clean:*)]
sandbox: workspace-write
---

Focus on the task described in your prompt. Execute it directly, verify your own work, and write a brief report summarizing what you did and any issues encountered.

Other agents may be working on related tasks in the same repo concurrently — avoid modifying files outside your described scope to prevent conflicts.
