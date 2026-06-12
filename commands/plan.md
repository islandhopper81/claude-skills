# /plan — Feature & Task Planning

Create a structured development plan for the following: $ARGUMENTS

## Instructions

Read `CLAUDE.md` in the project root (if present) to understand project conventions,
Jira project key, branch naming, and test commands before drafting the plan.

Produce a plan using the structure below. Be specific — vague plans make poor tickets.

---

## Plan Structure to Output

### Summary
One paragraph describing what is being built, why it is needed, and what problem it solves.

### Acceptance Criteria
A checklist of specific, testable, observable outcomes. Each item should be verifiable
by either an automated test or a defined manual check.

- [ ] ...
- [ ] ...

### Technical Approach
How the feature will be implemented. Include:
- Files, modules, or components to be created or modified
- Key logic or algorithms involved
- Any third-party libraries or APIs being used
- Data model changes (if applicable)

### Test Strategy
- **Unit tests**: What will be tested and how. Name specific functions or modules if known.
- **Integration tests**: Any cross-component or API-level tests needed.
- **Manual / user testing checklist**: Step-by-step actions the developer or user should
  take to validate the feature works end-to-end.

### Documentation Impact
Identify which documents in `/docs` or `README.md` will likely need updating as a result
of this change. Be specific (e.g., "README installation section", "/docs/architecture.md
data flow diagram").

### Open Questions
List anything that needs clarification before or during implementation. Flag any
assumptions that are being made.

### Out of Scope
Explicitly state what is NOT being built in this ticket to prevent scope creep.

---

When the plan is complete, ask:
"Does this plan look acceptable, or would you like to revise anything before I create the Jira ticket?"

Do not proceed to ticket creation until the user explicitly approves the plan.
