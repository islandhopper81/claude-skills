# /close-ticket — Add Implementation Summary and Move to Done

Usage: `/close-ticket` or `/close-ticket AEN-87 "optional notes"`

Wrap up the ticket by documenting what happened, surfacing follow-up items, and transitioning it to Done. Auto-detects GitHub, Linear, or Jira from `CLAUDE.md`.

## Instructions

### Step 1 — Detect issue tracker

Read `CLAUDE.md` in the project root. Look for an `issue_tracker` field:
- `issue_tracker: github` → use the GitHub CLI (`gh issue comment`, `gh issue close`)
- `issue_tracker: linear` → use Linear MCP tools (`save_comment`, `save_issue` with `state: "Done"`)
- `issue_tracker: jira` → use Atlassian MCP tools (`addCommentToJiraIssue`, `transitionJiraIssue`)
- If not present, infer from the ticket ID format:
  - Bare number or `#`-prefixed number (e.g. `12`, `#12`), or a github.com-only remote → assume **GitHub**
  - All-caps team prefix + number (e.g. `AEN-87`, `ENG-12`) with no Jira project key in `CLAUDE.md` → assume **Linear**
  - If `jira_project_key` is defined in `CLAUDE.md` → assume **Jira**
- If still ambiguous, ask: "Is this a GitHub, Linear, or Jira ticket?"

### Step 2 — Determine the ticket ID

- If provided as an argument, use it.
- Otherwise, use the ticket ID from the current session (e.g. inferred from the branch name `username/aen-NN-...`).
- If neither is available, ask the user.

### Step 3 — Gather implementation context from the current session

- The original plan (what was intended)
- Test results (what passed)
- Any documentation that was updated
- The PR number and URL (if `/pr` was run)
- Any optional notes provided as arguments

### Step 4 — Compose a closing comment

Use this structure:

```
## Implementation Summary

**PR**: {PR URL or "Not yet merged"}
**Branch**: {branch name}

### What Was Implemented
{Concise description of what was built, in past tense. 2–4 sentences.}

### Test Outcome
{Summary of test results — passed, any known failures left, manual testing done.}

### Documentation Updates
{List of docs updated, or "No documentation changes in this implementation."}

### Deviations from Original Plan
{Any changes made during implementation that differ from the plan. If none: "Implementation
followed the original plan without significant deviation."}

### Notes for Future Work
Actively scan the implementation for follow-up candidates. Check for each of:
- **Deferred work**: anything punted to "future" — out-of-scope items the ticket explicitly skipped, edge cases left unhandled, validations not added.
- **Shortcuts taken**: workarounds, TODO comments left in code, error handling that was minimized to ship.
- **Tests skipped or weakened**: cases not covered, mocks that hide real failure modes, manual checks that should be automated.
- **New tech debt**: temporary stubs, hardcoded values that should be configurable, inconsistencies introduced.
- **Adjacent issues discovered**: bugs in nearby code seen but not fixed, naming or structural drift, dead code.

For each item, write one line: `- **<short label>** [Critical / High / Medium / Low]: <one-sentence description, including file:line if relevant>`.

Use this scale:
- **Critical** — blocks a user flow, causes data loss or security exposure, or will break as soon as another ticket touches this code
- **High** — meaningful user or developer impact; should be addressed in the next cycle
- **Medium** — noticeable debt or gap, but workarounds exist; can be scheduled normally
- **Low** — minor polish, cleanup, or nice-to-have; safe to defer indefinitely

If genuinely none after scanning all five categories, write: "No follow-up items identified after scanning."
```

### Step 5 — Post the comment

**GitHub:** Write the composed comment to a temp file and post it: `gh issue comment <N> --body-file <tmpfile>`.

**Linear:** Use the Linear MCP `save_comment` tool with `issueId` set to the ticket ID and `body` set to the composed comment.

**Jira:** Use the Atlassian MCP `addCommentToJiraIssue` tool.

### Step 6 — Transition the ticket to Done

**GitHub:** Close the issue and move its board card to Done:
1. `gh issue close <N>` — closing auto-drops the card out of the active columns.
2. If the issue is on a Projects board, also set its `Status` field to **Done** so the board stays accurate (closing alone does not set Status). Resolve the project from `CLAUDE.md` (`github_project`) or `gh project list --owner <owner>`, then:
   - Find the item id for this issue: `gh project item-list <project-number> --owner <owner> --format json` (match `.content.number == <N>`).
   - Read the `Status` field id and its `Done` option id: `gh project field-list <project-number> --owner <owner> --format json`.
   - Set it: `gh project item-edit --project-id <PID> --id <ITEM_ID> --field-id <STATUS_FIELD_ID> --single-select-option-id <DONE_OPTION_ID>`.
   If the issue is not linked to any project, skip this sub-step (the `gh issue close` in step 1 is sufficient).

**Linear:** Use the Linear MCP `save_issue` tool with `id` set to the ticket ID and `state` set to `"Done"`.

**Jira:** Use the Atlassian MCP `transitionJiraIssue` tool. (You may need to call `getTransitionsForJiraIssue` first to find the correct transition ID.)

### Step 7 — Surface follow-up items to the user separately

After the ticket is closed, split the "Notes for Future Work" items by impact and output them under two separate headings:

**"Needs immediate action"** — Critical and High items only. These should become tickets before or during the next sprint.

**"Lower priority follow-ups"** — Medium and Low items. Listed for completeness but safe to defer.

Omit a heading entirely if it has no items.

End with: "Want me to create tickets for any of these?"

If the "Notes for Future Work" section was "No follow-up items identified after scanning," skip this step entirely — do not print an empty list.

### Step 8 — Report closure

- Confirmation the comment was added
- Confirmation the ticket is now in Done state
- Final prompt: "Ticket {ID} is closed. The branch is ready to merge via the open PR."
