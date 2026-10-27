---
name: branch
description: "Create a ticket branch in a safe working directory using the current project's branch and setup conventions."
---

Read ../../references/project-context.md relative to this skill before acting. Resolve sibling workflows by reading ../<name>/SKILL.md relative to this skill, not by assuming slash commands exist. Treat supplied user arguments as input; in a composed workflow reuse the caller's ticket and context. When called by ship, return control without prompting for the next command. Use available host tools or authenticated service connectors; Claude tool names are examples, not requirements.

# branch

Use the caller's ticket or fetch it once. Resolve branch type and slug from project conventions and ticket type/labels. Resolve and fetch the base branch. Inspect git status and worktrees; preserve unrelated changes and reuse an existing ticket worktree only after verifying it belongs to this work.
In a confirmed isolated cloud checkout create the branch in place. Otherwise create a worktree at ../<repo-name>-<ticket-key> from origin/<base>. Do not require a Claude-specific tool or environment variable.
Set all subsequent calls' working directory explicitly. Follow the project's Worktree Setup/dependency recipe. Give Node worktrees their own dependencies; do not introduce shared node_modules junctions. For Python prefer a worktree-local environment; if the project deliberately shares one, verify imports resolve to worktree code.
Push the ticket branch and establish tracking. Return branch, base and worktree paths to the caller. When standalone, report them to the user. Never delete a dirty worktree or force cleanup.
