# /refine-ticket — Refine a Ticket for AI Agent Implementation

Usage: `/refine-ticket [TICKET-ID]` (e.g., `/refine-ticket AEN-87` or `/refine-ticket FTF-42`)

Refine a ticket so it is unambiguous and immediately implementable by a senior software engineer AI agent, without needing follow-up clarification. Auto-detects Linear or Jira from `CLAUDE.md`.

## Instructions

### Step 1 — Detect issue tracker

Read `CLAUDE.md` in the project root. Look for an `issue_tracker` field:
- `issue_tracker: linear` → use Linear MCP tools
- `issue_tracker: jira` → use Jira/Atlassian MCP tools
- If not present, infer from the ticket ID format:
  - All-caps team prefix + number (e.g. `AEN-87`, `ENG-12`) with no Jira project key in `CLAUDE.md` → assume **Linear**
  - If `jira_project_key` is defined in `CLAUDE.md` → assume **Jira**
- If still ambiguous, ask: "Is this a Linear or Jira ticket?"

### Step 2 — Fetch the ticket

If a ticket ID is provided as `$ARGUMENTS`, use it. Otherwise ask: "Which ticket would you like to refine?"

**Linear:** Use the Linear MCP `get_issue` tool.
**Jira:** Use the Atlassian MCP `getJiraIssue` tool.

### Step 3 — Read project context

Read `CLAUDE.md` in the project root (if present) to understand:
- Tech stack, conventions, and file layout
- Test commands and patterns
- Branch naming and PR workflow

### Step 4 — Multi-expert analysis

Analyze the ticket from each of the following perspectives. For each expert, identify **gaps, ambiguities, and risks** in the current ticket — do not yet write the refined ticket, just surface issues.

---

#### Business Analyst
- Is the business value or user benefit stated clearly?
- Are acceptance criteria present, specific, and measurable — not vague ("it works") but observable ("returns 200 with field X")?
- Are there untested assumptions about user behavior?
- Is success defined in a way a non-engineer could verify?
- What is the cost of *not* doing this — to users, the product, or the business?

#### Product Manager
- Is scope tightly bounded? Is there a risk of scope creep?
- Is everything out of scope explicitly listed?
- Are there dependencies on other tickets or features that must be noted?
- Does the ticket title accurately reflect what will be built?
- Are there any UX or copy decisions left unresolved that would block implementation?
- Is the urgency or priority rationale documented? What is blocked or degraded if this slips?

#### Software Architect
- Does this fit the existing architecture (patterns, layers, data flow)?
- Does it introduce new dependencies or coupling that should be flagged?
- Are there data model concerns (schema changes, migrations needed)?
- Are there performance, scalability, or security implications?
- Does the ticket specify which modules/files are affected, or leave that ambiguous?
- What technical debt or system fragility accumulates if this is deferred?
- **Does this change deprecate, supersede, rename, or replace any existing field, function, endpoint, or schema?** If so, who reads or writes the old shape today? Are those call sites addressed in the implementation plan, or queued as a follow-up ticket? Orphaned readers of deprecated code are a common source of post-merge bugs — surface them now, not at runtime.

#### Software Engineer
- Is every implementation step specific enough to act on without guessing?
- Are function signatures, API contracts, or data shapes defined where needed?
- Are edge cases, error states, and failure modes covered?
- Are there technical prerequisites (migrations, env vars, config) that must happen first?
- Would an AI agent be able to write code directly from this ticket without asking a follow-up question?

#### Testing Engineer
- Are acceptance criteria testable via automated tests?
- Are specific test cases (happy path, error cases, boundary conditions) described?
- What needs to be mocked or stubbed? Is that specified?
- Are integration test scenarios identified?
- Is there a manual testing checklist for anything not coverable by automated tests?

---

### Step 5 — Synthesize a refined ticket

Using the gap analysis above, produce a complete refined ticket in the following structure. Fill in gaps using reasonable inferences from the codebase context and project conventions — do not leave blanks. If something is genuinely unknowable without user input, flag it in **Open Questions**.

---

```
## Summary
[1–2 sentences: what is being built, why, and what user/system problem it solves.]

## Background & Context
[Any relevant architecture context, prior tickets this builds on, or decisions already locked in.
Include references to specific files or modules the implementer should read first.]

## Motivation & Risk
**Why this change is necessary:**
[The specific problem, gap, or constraint that makes this work required — not just what it does,
but why it must be done now and what is driving it.]

**Impact of the change:**
[What improves, unblocks, or becomes possible once this is implemented. Be concrete —
e.g., "enables X feature," "removes Y bottleneck," "satisfies Z compliance requirement."]

**What breaks or degrades without it:**
[The specific negative outcome if this ticket is skipped or deferred — data corruption,
broken user flow, security gap, blocked dependent ticket, accumulating tech debt, etc.
If the answer is "nothing urgent," state that honestly.]

## Implementation Plan
[Ordered, specific steps. Name files, functions, and data structures explicitly.
Each step should be actionable without further clarification.

If the Architect analysis flagged this ticket as deprecating, superseding, renaming,
or replacing an existing field/function/endpoint/schema, include a final step that
sweeps the codebase for stale readers and writers (use the Grep tool with a concrete
pattern, scoped to `src/` or equivalent). State the expected results inline so the
implementer can verify completeness — either migrate the stale sites in this ticket
or queue them as a candidate follow-up ticket in the PR description.]

1. ...
2. ...

## API / Data Contract Changes
[If applicable: new endpoints, changed request/response shapes, schema migrations, env vars.
If none: "No API or data contract changes."]

## Acceptance Criteria
[Specific, testable, binary-pass/fail items. Each item must be verifiable.]

- [ ] ...
- [ ] ...

## Test Requirements
- **Unit tests**: [Name specific functions/modules and what to assert]
- **Integration tests**: [Cross-layer or API-level tests, what to mock]
- **Manual checks**: [Step-by-step actions to validate end-to-end]

## Out of Scope
[Explicitly list what is NOT being built in this ticket.]

- ...

## Dependencies & Prerequisites
[Other tickets that must merge first, env vars that must exist, services that must be running.]

## Open Questions
[Anything that requires product or engineering decision before implementation can proceed.
If none: omit this section.]
```

---

### Step 6 — Present and confirm

Show the refined ticket to the user and ask:

> "Does this refinement look accurate, or would you like to adjust anything before I update the ticket?"

Do not update the tracker until the user explicitly approves.

### Step 7 — Update the tracker and apply label

Once approved:

**Linear:**
1. Update the ticket description using the Linear MCP `save_issue` tool.
2. Apply the `refined` label using the `labels` field in `save_issue` (e.g. `labels: ["refined"]`).
   - If the label does not exist, create it first using `create_issue_label` with color `#7C3AED` and description `"Ticket has been refined and is ready for implementation"`, then apply it.

**Jira:**
1. Update the ticket description using the Atlassian MCP `editJiraIssue` tool.
2. Apply a `refined` label using the labels field in `editJiraIssue`.

Report back: "Ticket [ID] has been updated and is ready for implementation."
