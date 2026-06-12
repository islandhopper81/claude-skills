# /close-ticket — Add Implementation Summary and Move to Done

Usage: `/close-ticket` or `/close-ticket AEN-87 "optional notes"`

Wrap up the ticket by documenting what happened, surfacing follow-up items, and transitioning it to Done. Auto-detects Linear or Jira from `CLAUDE.md`.

## Instructions

### Step 1 — Detect issue tracker

Read `CLAUDE.md` in the project root. Look for an `issue_tracker` field:
- `issue_tracker: linear` → use Linear MCP tools (`save_comment`, `save_issue` with `state: "Done"`)
- `issue_tracker: jira` → use Atlassian MCP tools (`addCommentToJiraIssue`, `transitionJiraIssue`)
- If not present, infer from the ticket ID format:
  - All-caps team prefix + number (e.g. `AEN-87`, `ENG-12`) with no Jira project key in `CLAUDE.md` → assume **Linear**
  - If `jira_project_key` is defined in `CLAUDE.md` → assume **Jira**
- If still ambiguous, ask: "Is this a Linear or Jira ticket?"

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

For each item, write one line: `- **<short label>**: <one-sentence description, including file:line if relevant>`.

If genuinely none after scanning all five categories, write: "No follow-up items identified after scanning."
```

### Step 5 — Post the comment

**Linear:** Use the Linear MCP `save_comment` tool with `issueId` set to the ticket ID and `body` set to the composed comment.

**Jira:** Use the Atlassian MCP `addCommentToJiraIssue` tool.

### Step 6 — Transition the ticket to Done

**Linear:** Use the Linear MCP `save_issue` tool with `id` set to the ticket ID and `state` set to `"Done"`.

**Jira:** Use the Atlassian MCP `transitionJiraIssue` tool. (You may need to call `getTransitionsForJiraIssue` first to find the correct transition ID.)

### Step 7 — Surface follow-up items to the user separately

After the ticket is closed, output the contents of the "Notes for Future Work" section back to the user as a standalone list under the heading **"Suggested follow-up tickets"**. This makes them actionable without re-reading the closing comment.

End the list with: "Want me to create tickets for any of these?"

If the "Notes for Future Work" section was "No follow-up items identified after scanning," skip this step entirely — do not print an empty list.

### Step 8 — Report closure

- Confirmation the comment was added
- Confirmation the ticket is now in Done state
- Final prompt: "Ticket {ID} is closed. The branch is ready to merge via the open PR."
