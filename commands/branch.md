# /branch -- Create a Git Branch for a Ticket

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
   - If the ticket was already fetched earlier in this session (e.g., by `/ship`), use that
     data -- **do not fetch the ticket again**.
   - Otherwise, fetch the ticket to read the summary, then derive a slug (max 4-5 words,
     lowercase, hyphens).
   - Example: "Add ingredient substitution suggestions" -> `ingredient-substitution-suggestions`

4. **Create the branch and worktree** together from the default branch:
   ```
   git fetch origin
   git worktree add -b {branch-name} ../{repo-name}-{TICKET-ID} origin/{default-branch}
   ```
   Where `{repo-name}` is the current directory name (e.g. `FeedTheFamily`).
   This creates the branch and a linked working directory simultaneously --
   the current checkout is not disturbed.

5. **Install node_modules** in the new worktree by running `npm install` directly in each
   package directory. Do NOT create directory junctions or symlinks pointing at the main
   repo's `node_modules` — junctions are fragile on Windows: if the main repo's
   `node_modules` is ever impaired (e.g. from a root-level `npm install` mistake or a
   prior bad teardown), every worktree sharing it breaks too.

   ```powershell
   cd "..\{repo-name}-{TICKET-ID}\backend"; npm install
   cd "..\{repo-name}-{TICKET-ID}\frontend"; npm install
   ```

   With npm's local cache these installs typically complete in under 60 seconds each.

6. **Push the branch** to the remote to establish tracking:
   ```
   cd ../{repo-name}-{TICKET-ID} && git push -u origin {branch-name}
   ```

7. **Report back** with:
   - The full branch name created
   - The worktree path (e.g. `../FeedTheFamily-FF-42`)
   - Confirmation the branch is pushed and tracking
   - Prompt: "Branch and worktree are ready at `../{repo-name}-{TICKET-ID}`. Open that directory to begin implementation, then run `/test` when ready to validate."

Do not create the worktree if a worktree for that branch already exists --
check with `git worktree list` first and warn the user if so.

---

## Worktree Teardown (when deleting the worktree)

Because the worktree has its own real `node_modules` directories (not junctions),
`Remove-Item -Recurse` is safe to run directly on Windows:

```powershell
# Delete the worktree directory
Remove-Item -Recurse -Force "..\{repo-name}-{TICKET-ID}"

# Prune the git worktree reference
git worktree prune

# Delete the local branch
git branch -d {branch-name}
```

**macOS / Linux:**
```bash
git worktree remove "../{repo-name}-{TICKET-ID}" --force
git branch -d {branch-name}
```
