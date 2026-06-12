# /merge — Merge a Pull Request

Merge the pull request for the current branch into the project's default branch.

## Instructions

1. **Determine the default base branch**:
   - Read `CLAUDE.md` for a `Default Base Branch` field (or equivalent).
   - If not defined, fall back to `main`, then `master`.
   - Use this value everywhere this skill references "the default branch".

2. **Identify the PR**:
   - If a PR number is provided as an argument (e.g., `/merge 42`), use that.
   - Otherwise, find the open PR for the current branch:
     ```
     gh pr view --json number,headRefName,baseRefName,mergeable,statusCheckRollup
     ```
   - If no open PR exists for the current branch, warn the user and stop.

3. **Verify the PR is mergeable**:
   - Confirm `mergeable` is `MERGEABLE` (not `CONFLICTING` or `UNKNOWN`).
   - If CI status checks exist, confirm they are all passing. If any are failing
     or still running, surface that to the user and ask before proceeding —
     do not auto-merge over red CI.
   - If the PR has unresolved review comments or change requests, warn the user
     before merging.

4. **Determine the merge strategy** by inspecting recent merges into the default branch:
   ```
   git log {default-branch} --merges --oneline -5
   ```
   - If recent commits are merge commits (`Merge pull request #N from ...`),
     use `--merge` (preserves the feature branch history).
   - If recent commits are flat (no merges), the project likely squashes — use
     `--squash`.
   - If `CLAUDE.md` specifies a merge strategy, honor that over the heuristic.
   - If still ambiguous, ask the user.

5. **Merge and delete the branch**:
   ```
   gh pr merge {PR-NUMBER} --{strategy} --delete-branch
   ```
   This deletes the branch on both the remote and locally (if checked out).

6. **Sync the local default branch**:
   ```
   git checkout {default-branch}
   git pull
   ```
   So the working tree reflects the merged state and is ready for the next branch.

7. **Remove the worktree** if one exists for the merged branch:
   ```
   git worktree list
   ```
   Look for an entry whose branch matches the one just merged. If found, remove it:
   ```
   git worktree remove ../{worktree-path}
   ```
   If the worktree has uncommitted changes, warn the user and ask before removing —
   do not force-remove without explicit approval.
   If no worktree exists for this branch, skip silently.

8. **Report back** with:
   - The merge commit hash (short)
   - The strategy used
   - The default branch name (so the user sees which branch they were merged into)
   - Confirmation the branch was deleted
   - Confirmation the worktree was removed (or that none existed)
   - Prompt: "Merged into {default-branch}. Run `/close-ticket` to mark the ticket Done,
     or start the next ticket with `/implement-ticket {TICKET-ID}`."

## Safety

- Never run `gh pr merge --admin` (bypassing branch protection) unless the user
  explicitly asks. If the merge is blocked, surface the reason and stop.
- Never force-merge over failing required checks without explicit user approval.
- Never merge a PR that is still in draft state — surface that and ask first.
