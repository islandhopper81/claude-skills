# /branch — Create a Git Branch for a Ticket

Create a properly named git branch tied to a Jira ticket.

Usage: `/branch` (uses ticket ID from this session) or `/branch FTF-42 short-description`

## Instructions

1. **Determine the ticket ID**:
   - If provided as an argument (e.g., `FTF-42`), use that.
   - Otherwise, use the ticket ID created in the current session by `/ticket`.
   - If neither is available, ask the user for the ticket ID before proceeding.

2. **Read branch naming conventions** from `CLAUDE.md`. If not defined, use this default:
   - `feat/{TICKET-ID}-{short-description}` for Stories
   - `fix/{TICKET-ID}-{short-description}` for Bugs
   - `chore/{TICKET-ID}-{short-description}` for Tasks
   - `spike/{TICKET-ID}-{short-description}` for Spikes

3. **Derive the short description**:
   - If provided as an argument, use it (lowercase, hyphen-separated).
   - Otherwise, derive it from the ticket summary (max 4–5 words, lowercase, hyphens).
   - Example: "Add ingredient substitution suggestions" → `ingredient-substitution-suggestions`

4. **Check out the branch** from the current default branch (`main` or `master`):
   ```
   git checkout main && git pull origin main
   git checkout -b {branch-name}
   ```

5. **Push the branch** to the remote to establish tracking:
   ```
   git push -u origin {branch-name}
   ```

6. **Report back** with:
   - The full branch name created
   - Confirmation the branch is pushed and tracking
   - Prompt: "Branch is ready. Begin implementation, then run `/test` when ready to validate."

Do not create the branch if there are uncommitted changes on the current branch —
warn the user and ask how to proceed.
