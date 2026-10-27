---
name: updatedocs
description: "Review a branch diff and update affected project documentation; apply directly within an authorized implementation workflow."
---

Read ../../references/project-context.md relative to this skill before acting. Resolve sibling workflows by reading ../<name>/SKILL.md relative to this skill, not by assuming slash commands exist. Treat supplied user arguments as input; in a composed workflow reuse the caller's ticket and context. When called by ship, return control without prompting for the next command. Use available host tools or authenticated service connectors; Claude tool names are examples, not requirements.

# updatedocs

Resolve the base branch and review both committed and uncommitted intended changes. Read affected docs before editing. Update the project guide, README or docs for changes to interfaces, schema, setup, environment, architecture or user-visible behavior. Follow any project Documentation Impact plan.
Within ship or an explicitly authorized edit request, apply the updates and return what changed without an extra approval pause. If the user asks only for proposals, show proposals without writing. If no update is needed, state why. Return to a composing caller without prompting for the next command.
