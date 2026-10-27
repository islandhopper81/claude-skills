# Shared project and execution context

Read the target project's AGENTS.md and CLAUDE.md, including their referenced guidance. These are project files, not files in the installed plugin. Follow applicable instructions and resolve conflicts using the host instruction hierarchy. Do not change project policy to fit this plugin.

Resolve the base branch from Default Base Branch or equivalent project configuration; otherwise query the remote default branch. Use that one value for branching, diffs, logs and PR targets. Never silently assume main or develop.

Resolve the issue tracker from issue_tracker, Jira Project Key/jira_project_key, linear_team and project context. A bare issue number requires repository context. Use GitHub, Jira or Linear connectors or authenticated CLIs actually available. If ambiguous, ask. Retrieve ticket labels, plan, acceptance criteria, test requirements and dependency links as needed. Cache ticket data during the workflow; refresh only if necessary to verify changed state.

Respect the user's authorized scope. Calling ship authorizes implementation, tests, documentation, commit, push, PR creation and ticket progress updates. Merging and deployment require user authorization; existing authorization in the session counts. A plugin cannot override host permissions.

Use an isolated checkout in place only when exclusivity is confirmed. Otherwise use a ticket worktree without disturbing another checkout. Set each shell call's working directory explicitly or use git -C. No EnterWorktree/ExitWorktree tool is required. Windows paths require a compatible shell and quoting; use POSIX equivalents elsewhere.

For each project's dependency setup and verification, follow its guide. Do not hardcode packages, test commands, schema rules or credentials in this plugin. Never display or commit secrets. Prefer project checks over generic defaults.

When composing workflows, carry a shared context: ticket, tracker, repo, base branch, working directory, changed files, checks already run, PR and authorization. Helpers return results to the caller without next-command prompts or redundant tests. Standalone helpers may suggest a next action.

Leave tickets In Progress while their PR is open. Mark Done only after verifying the associated PR merged. Report actual CI state; monitor during the active task without claiming a persistent subscription. Stop on a blocker with a concise account of completed work.
