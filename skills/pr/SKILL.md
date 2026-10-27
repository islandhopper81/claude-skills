---
name: pr
description: "Open a pull request into the project's resolved base branch with verified acceptance criteria and test evidence."
---

Read ../../references/project-context.md relative to this skill before acting. Resolve sibling workflows by reading ../<name>/SKILL.md relative to this skill, not by assuming slash commands exist. Treat supplied user arguments as input; in a composed workflow reuse the caller's ticket and context. When called by ship, return control without prompting for the next command. Use available host tools or authenticated service connectors; Claude tool names are examples, not requirements.

# pr

Resolve the base using shared project context and use it consistently for git log, git diff and the PR target. Stop if the current branch equals that base. Read the current diff and commits; use verified implementation context to write Summary, Ticket, Changes, Acceptance Criteria, Testing, Documentation and Follow-ups as applicable. Check criteria only when verified.
Use an available GitHub connector or gh pr create --base <base>. For CLI multiline bodies use a temporary file and --body-file with proper shell quoting. Link GitHub issues with Closes #N; link Jira/Linear tickets using their actual URLs and project integration conventions.
Return the PR URL and actual state. Leave the ticket In Progress until merge. When invoked by ship return directly; do not prompt to close an unmerged ticket.
