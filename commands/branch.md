# /branch -- Create a Git Branch for a Ticket

Create a properly named git branch tied to a ticket (GitHub, Jira, or Linear).

Usage: `/branch` (uses ticket ID from this session) or `/branch FTF-42 short-description` (GitHub issues use the bare number, e.g. `/branch 12 short-description`)

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

   The prefix is chosen from the ticket's type. GitHub issues have no "type" field —
   infer the prefix from the issue's labels (`bug` → `fix/`; `chore`/`infra` → `chore/`;
   otherwise `feat/`), and use the bare issue number as `{TICKET-ID}` (e.g. `feat/12-config-adapter`).

3. **Derive the short description**:
   - If provided as an argument, use it (lowercase, hyphen-separated).
   - If the ticket was already fetched earlier in this session (e.g., by `/ship`), use that
     data -- **do not fetch the ticket again**.
   - Otherwise, fetch the ticket to read the summary, then derive a slug (max 4-5 words,
     lowercase, hyphens). Fetch via the detected tracker — GitHub:
     `gh issue view <N> --json title,labels`; Linear/Jira: their MCP fetch tools.
   - Example: "Add ingredient substitution suggestions" -> `ingredient-substitution-suggestions`

4. **Create the branch and worktree** together from the default branch.
   **On Windows use the PowerShell tool for this step** — the Bash tool treats `\` as an
   escape character, causing the worktree path to resolve inside the current directory
   instead of the parent. Use forward slashes in the path argument; Git on Windows accepts
   them in both tools.

   ```powershell
   git fetch origin
   git worktree add -b {branch-name} "../{repo-name}-{TICKET-ID}" origin/{default-branch}
   ```

   Where `{repo-name}` is the current directory name (e.g. `FeedTheFamily`).
   This creates the branch and a linked working directory simultaneously --
   the current checkout is not disturbed.

5. **Install dependencies** in the new worktree. **Use the PowerShell tool for this step
   on Windows.** Do not assume a fixed layout — projects differ (e.g. FeedTheFamily has
   `backend/` + `frontend/`; ProductOne/spec has a Python backend in `src/` and a Next.js
   frontend in `webapp/`). If `CLAUDE.md` defines a "Worktree Setup" recipe, follow it.
   Otherwise, per package manager present:

   **Node packages** — run `npm install` in *every directory that contains a
   `package.json`* (detect them; do not hardcode `backend`/`frontend`).

   ```powershell
   # Example — repeat for each package.json directory the repo actually has
   cd "..\{repo-name}-{TICKET-ID}\{package-dir}"; npm install
   ```

   Do NOT create directory junctions or symlinks pointing at the main repo's
   `node_modules` — junctions are fragile on Windows: if the main repo's `node_modules`
   is ever impaired (e.g. from a root-level `npm install` mistake or a prior bad
   teardown), every worktree sharing it breaks too. With npm's local cache a fresh
   install typically completes in under 60 seconds per package.

   **Python backend** — the worktree does **not** get its own virtualenv; it shares the
   main checkout's `.venv`. Do not run a package install here. Note the resolution
   caveat: because that venv is an *editable install*, `import <pkg>` resolves to the
   **main checkout**, not the worktree. When you later run venv-backed tools against
   worktree code (alembic, code generation, dump scripts), set `PYTHONPATH` to the
   worktree root so they exercise the worktree's code, and verify with
   `python -c "import <pkg>; print(<pkg>.__file__)"`. (`pytest` is the exception — it
   prepends the worktree root itself when the test directory is a package.)

6. **Push the branch** to the remote to establish tracking (PowerShell tool):
   ```powershell
   cd "..\{repo-name}-{TICKET-ID}"; git push -u origin {branch-name}
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

Because the worktree owns its own dependency directories if any (real `node_modules`,
not junctions into the main checkout) and shares no virtualenv with it,
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
