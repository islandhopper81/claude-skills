---
name: ship
description: "Implement a refined GitHub, Jira or Linear ticket, verify it, update docs, open a PR and follow CI. Does not implicitly authorize merge or deployment."
---

Read ../../references/project-context.md relative to this skill before acting. Resolve sibling workflows by reading ../<name>/SKILL.md relative to this skill, not by assuming slash commands exist. Treat supplied user arguments as input; in a composed workflow reuse the caller's ticket and context. When called by ship, return control without prompting for the next command. Use available host tools or authenticated service connectors; Claude tool names are examples, not requirements.

# ship

1. Resolve project context and fetch the ticket once, including readiness labels and blockers. Require an Implementation Plan and Acceptance Criteria. If the project defines a refined label, require it (FeedTheFamily uses refined). Verify blocking tickets are finished; stop on missing readiness or unresolved blockers.
2. Move the ticket to In Progress. Read the branch sibling workflow and establish an isolated working directory.
3. Read implement-ticket and implement the plan. Pass the cached ticket and skip its standalone verification/handoff: ship owns verification once.
4. Read test and run the required checks for the changed packages plus project-required wider checks. Run the frontend build when relevant and project-mandated migration/schema validation. Fix failures; never weaken tests to get green. Repeat checks only after relevant changes or unresolved failures.
5. Review the full diff against the resolved base for scope, debug leftovers and acceptance criteria. Read updatedocs and apply relevant documentation changes within ship's authorization. Record when none are needed.
6. Read commit and pr; commit only intended changes, push and open a PR into the resolved base. Include ticket link, changes, verified acceptance criteria, exact checks/results, manual gaps and follow-ups.
7. Comment on the ticket with the PR and implementation summary; leave it In Progress. Check CI and review activity during the task, fix failures and actionable review findings, and rerun affected checks. Report blockers and pending checks honestly.
8. Do not merge or deploy without authorization. If the PR is merged within the active task, read close-ticket to verify merge and mark Done, then safely clean up the worktree. Otherwise hand off the PR and actual CI state; do not close the ticket or pretend to monitor after the task ends.
