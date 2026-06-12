---
name: ship
description: Full development cycle — branch, implement, test, docs, commit, PR, close ticket. Runs end-to-end without pausing for confirmation.
---

# /ship — Ship a Ticket End-to-End

Usage: `/ship TICKET-ID` (e.g., `/ship AEN-87` or `/ship FF-42`)

Run the full development cycle for a ticket autonomously. The user has opted into
end-to-end execution — do not pause for confirmation between steps.

---

## Step 1 — Branch

Invoke the `branch` skill with the provided ticket ID.

- Read branch naming conventions from `CLAUDE.md`
- Check out a new branch from the default base branch
- Push the branch to establish remote tracking

If there are uncommitted changes on the current branch, stop and report before proceeding.

---

## Step 2 — Implement

Invoke the `implement-ticket` skill with the provided ticket ID.

- Fetch the ticket from the issue tracker (auto-detected from `CLAUDE.md`)
- Explore the codebase, read all relevant files
- Implement the changes exactly as specified in the ticket's implementation plan
- Write or update tests as required by the ticket
- **Skip the Phase 5 Handoff** — do not prompt the user at the end of `implement-ticket`; return control here and continue to Step 3.

---

## Step 3 — Test

Invoke the `test` skill.

- Run the full test suite using the commands defined in `CLAUDE.md`
- If any tests fail, diagnose and fix them before continuing
- Do not proceed to the next step until all tests pass

---

## Step 4 — Update Docs

Invoke the `updatedocs` skill.

- Diff the branch against the base branch to identify what changed
- Infer which documentation files are affected
- Draft and apply updates to `README.md`, `/docs`, or other relevant files
- If no documentation changes are needed, note that and continue

---

## Step 5 — Commit

Invoke the `commit` skill.

- Stage all changes
- Generate a conventional commit message from the diff
- Commit and push to the remote tracking branch

---

## Step 6 — Pull Request

Invoke the `pr` skill.

- Generate a PR description (summary, changes, acceptance criteria checklist, testing notes)
- Open the PR targeting the default base branch
- Report the PR URL

---

## Step 7 — Close Ticket

Invoke the `close-ticket` skill with the ticket ID.

- Post an implementation summary comment on the ticket
- Transition the ticket to Done
- Surface any follow-up items identified during implementation

---

## Error Handling

If any step fails or produces an error, stop immediately and report:
- Which step failed
- The error or blocker
- What was completed successfully before the failure

Do not attempt to continue to the next step after a failure.
