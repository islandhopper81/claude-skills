# /create-ticket — Create a Ticket and Transition to In Progress

Using the approved plan from this session, create a ticket in the project's issue tracker and prepare it for development.

## Instructions

### Step 1 — Detect issue tracker

Read `CLAUDE.md` in the project root. Look for an `issue_tracker` field:
- `issue_tracker: linear` → use Linear MCP tools
- `issue_tracker: jira` → use Jira/Atlassian MCP tools
- If not present, infer from context:
  - `linear_team` defined in `CLAUDE.md` → assume **Linear**
  - `jira_project_key` defined in `CLAUDE.md` → assume **Jira**
- If still ambiguous, ask: "Is this a Linear or Jira project?"

### Step 2 — Determine issue type

Based on the nature of the plan:
- `Story` / `Feature` — a user-facing feature or capability
- `Task` — a technical chore, refactor, or infrastructure change
- `Bug` — a defect fix
- `Spike` — exploratory research with uncertain output

### Step 3 — Create the ticket

**Summary**: A concise, action-oriented title (e.g., "Add ingredient substitution suggestions to meal planner")

**Description**: The full approved plan, formatted cleanly. Include all sections:
Summary, Acceptance Criteria, Technical Approach, Test Strategy,
Documentation Impact, Open Questions, Out of Scope.

---

**Linear:**
1. Read `linear_team` from `CLAUDE.md` for the team identifier.
2. Create the issue using the Linear MCP `save_issue` tool with `title`, `description`, and `teamId`.
3. Transition the issue to **In Progress** using `save_issue` with the appropriate `stateId`.
   - Use `get_issue_status` or `list_issue_statuses` to find the In Progress state ID first.

**Jira:**
1. Read the `jira_project_key` from `CLAUDE.md`.
2. Create the ticket using the Atlassian MCP `createJiraIssue` tool.
3. Transition the ticket to **In Progress** using `transitionJiraIssue`.
   - Use `getTransitionsForJiraIssue` to find the In Progress transition ID first.

Do not guess the project key or team — read it from `CLAUDE.md`. If `CLAUDE.md` is missing or the value is not defined, ask the user before proceeding.

### Step 4 — Report back

- The ticket ID (e.g., `AEN-42` or `FTF-42`)
- The ticket URL
- Confirmation it was moved to In Progress

Then prompt:
> "Ready to create the branch. Run `/branch` to continue, or let me know the ticket ID if you want to do it manually."
