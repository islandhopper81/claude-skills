---
name: implement-ticket
description: Implement a Linear ticket end-to-end — branch, read ticket, explore codebase, write code, run tests.
---

# /implement-ticket — Implement a Linear Ticket

Usage: `/implement-ticket [TICKET-ID]` (e.g., `/implement-ticket AEN-87`)

Act as a senior software engineer. Read the ticket, understand the codebase, implement the changes precisely, and verify the work.

---

## Phase 1 — Setup

### Step 1 — Resolve the ticket ID

- If a ticket ID is provided as `$ARGUMENTS`, use it.
- Otherwise ask: "Which ticket would you like to implement? (e.g., AEN-87)"

### Step 2 — Read project conventions

Read `CLAUDE.md` in the project root. Note:
- Repo structure (which directories contain backend, frontend, tests)
- Tech stack and frameworks
- Test commands
- Branch naming convention and base branch

### Step 3 — Check git state

Run `git status` and `git branch --show-current`.

**If the current branch already matches this ticket** (branch name contains the ticket number), proceed to Phase 2.

**If on `develop` or `main` with no uncommitted work**, create the branch now:
1. `git fetch origin && git checkout develop && git pull`
2. Name the branch using the convention in `CLAUDE.md`. If not defined: `{username}/aen-{number}-{slug}` (max 5-word slug from the ticket title).
3. `git checkout -b {branch-name}`
4. `git push -u origin {branch-name}`

**If there are uncommitted changes**, stop and ask the user how to proceed before touching git.

---

## Phase 2 — Understand

### Step 4 — Fetch the ticket

**Detect issue tracker** — Read `CLAUDE.md` and look for an `issue_tracker` field:
- `issue_tracker: linear` → use Linear MCP tools
- `issue_tracker: jira` → use Jira/Atlassian MCP tools
- If not present, infer from context:
  - `linear_team` defined in `CLAUDE.md` → assume **Linear**
  - `jira_project_key` or `Jira Project Key` defined in `CLAUDE.md` → assume **Jira**
- If still ambiguous, ask: "Is this a Linear or Jira project?"

**Linear:** Fetch using the `get_issue` MCP tool.
**Jira:** Fetch using the Atlassian `getJiraIssue` MCP tool.

Read the entire ticket:
- Summary and background
- Implementation plan (treat it as the authoritative spec — follow it step by step)
- Acceptance criteria (these are your definition of done)
- Test requirements
- Out of scope (do not implement anything listed here)
- Dependencies and prerequisites (verify they are met before proceeding)

### Step 5 — Explore the codebase

Read every file named in the ticket's "Relevant files" or "Implementation Plan" sections. Then:
- Identify all files that will need to change
- Read the current content of each file in full
- Read related files for context (e.g., models referenced by the service being changed, existing tests for the module being modified)
- Run a targeted search (Grep) for any symbol, function, or pattern the ticket references that you haven't already read

Do not skip this step. Attempting implementation without reading the code produces incorrect results.

---

## Phase 3 — Implement

### Step 6 — Execute the implementation plan

Follow the ticket's Implementation Plan in the stated order. For each step:
- Make only the changes described — no refactoring, no scope creep, no "while I'm in here" improvements
- Prefer editing existing files over creating new ones
- Write no comments unless the code encodes a non-obvious constraint or workaround
- Do not add error handling for scenarios the ticket marks out of scope
- Match existing code style (naming, formatting, import ordering) in each file

After each logical group of changes (e.g., completing one step of the implementation plan), verify the change is internally consistent before moving to the next step.

### Step 7 — Write tests

Add or update tests as specified in the ticket's Test Requirements section.
- Place tests in the file and location identified by the ticket (or the project's existing test layout)
- Cover every test case named in the ticket — do not skip or abbreviate
- Follow existing test patterns in the file (fixtures, mocking approach, assertion style)
- Do not add tests for scenarios the ticket marks out of scope

---

## Phase 4 — Verify

### Step 8 — Run the test suite

Run the test command(s) from `CLAUDE.md` (e.g., `pytest`, `npm run build`).

- If tests pass: proceed to Step 9.
- If tests fail: diagnose and fix. Do not move forward with failing tests.
- If you cannot make a test pass and are unsure why, stop and explain the blocker to the user — do not paper over it.

### Step 9 — Self-review against acceptance criteria

Go through each acceptance criterion in the ticket one by one. For each item:
- Confirm it is satisfied by the code you wrote, OR
- Flag it explicitly if it requires a manual check the user must perform

Report the checklist to the user with pass/flag status for each item.

---

## Phase 5 — Handoff

### Step 10 — Report completion

Summarize:
- Files changed and what changed in each
- Tests added
- Any acceptance criteria that require manual verification
- Any out-of-scope items that came up during implementation (flag but do not implement)

Then prompt:
> "Implementation complete. Run `/commit` to stage and commit, or `/test` to re-run the test suite."
