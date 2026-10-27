---
name: close-ticket
description: "Summarize an implementation and mark its GitHub, Jira or Linear ticket Done only after verifying the associated PR merged."
---

Read ../../references/project-context.md relative to this skill before acting. Resolve sibling workflows by reading ../<name>/SKILL.md relative to this skill, not by assuming slash commands exist. Treat supplied user arguments as input; in a composed workflow reuse the caller's ticket and context. When called by ship, return control without prompting for the next command. Use available host tools or authenticated service connectors; Claude tool names are examples, not requirements.

# close-ticket

Resolve the tracker and ticket from shared context. Identify the associated PR and verify its merged state via the hosting service. If no PR exists or it is open/closed-unmerged, report that condition and leave the ticket unfinished. Do not treat PR creation as completion.
Compose and post an implementation summary with the merged PR URL, changes, checks, documentation, deviations, and concrete follow-ups by impact. Do not invent follow-ups.
Then transition the ticket to Done/closed using available tracker tools. For GitHub Projects, update the linked board status when configured and accessible; report partial failure without repeating successful writes.
Return the results to ship when composed. Standalone, report closure and outstanding follow-ups.
