# /test -- Run Tests and Summarize Results

Run the project test suite and provide an actionable summary.

## Instructions

1. **Read test commands** from `CLAUDE.md`. Look for a section defining unit test and
   integration test commands (e.g., `npm run test`, `pytest`, `nextflow run tests/`).
   If not defined in `CLAUDE.md`, inspect `package.json`, `Makefile`, or `pyproject.toml`
   to infer the correct test command before asking the user.

2. **Ensure the test environment is set up.**

   For Python projects (anything using `pytest`, `nextflow`, etc.), the test command
   must run inside the project virtualenv so backend dependencies resolve. Before running:

   - If `$VIRTUAL_ENV` is set, the venv is already active -- proceed.
   - If `$VIRTUAL_ENV` is unset and a `.venv/` directory exists at the project root,
     activate it first:
     - Windows PowerShell: `.\.venv\Scripts\Activate.ps1`
     - POSIX shells: `source .venv/bin/activate`
   - **If there is no local `.venv/` but the current directory is a linked git worktree**
     (as created by `/branch` and `/ship`), the venv lives in the **main checkout**, not
     here. Do not fall through to system Python. Find the main worktree with
     `git worktree list` (its first entry is the main checkout) and use *its* interpreter
     directly — e.g. `<main-checkout>\.venv\Scripts\python.exe -m pytest` (Windows) or
     `<main-checkout>/.venv/bin/python -m pytest` (POSIX). Because that venv is an editable
     install, also set `PYTHONPATH` to the **worktree root** so the project package resolves
     to the worktree's code, not the main checkout's:
     - Windows PowerShell: `$env:PYTHONPATH = "<worktree-root>"; & "<main-checkout>\.venv\Scripts\python.exe" -m pytest`
     - POSIX shells: `PYTHONPATH=<worktree-root> <main-checkout>/.venv/bin/python -m pytest`

     (`pytest` prepends the worktree root itself when the test directory is a package, so
     collection usually works even without `PYTHONPATH`; set it anyway to be safe for tools
     that don't, such as code generators or migration runners.)
   - If no `.venv/` exists anywhere (not a worktree, no main-checkout venv), run the test
     command as-is. If it then fails with `ModuleNotFoundError` for a project dependency
     (e.g. `No module named 'boto3'`), report the missing-venv diagnosis instead of treating
     it as a real test failure.

3. **Run the tests** and capture full output.

4. **If the output contains `Cannot find module` errors** (Node.js missing-dependency
   failures): the current `/branch` and `/ship` flow gives each worktree its own **real**
   `node_modules` (no junctions), so this is normally a genuine failure — treat it as one
   and continue to step 5. The one exception is a worktree created by an older,
   junction-based strategy; only in that case does the recovery below apply.

   Check whether the affected package's `node_modules` is a shared link (substitute the
   actual package directory — e.g. `webapp`, `backend`, `frontend` — for `{pkg}`):

   - **Windows:** `(Get-Item "{pkg}\node_modules").LinkType` -- returns "Junction" if linked
   - **macOS/Linux:** `test -L {pkg}/node_modules && echo linked`

   If the directory **is** a junction or symlink, the worktree is sharing node_modules
   from the main checkout and a new package is missing there. Recover automatically:

   a. Delete the junction/symlink using the .NET API (avoids shell hook interference):
      - Windows: `[System.IO.Directory]::Delete(".\{pkg}\node_modules")`
      - macOS/Linux: `unlink {pkg}/node_modules`

   b. Run `npm install` in the affected directory inside the worktree.

   c. Re-run the tests. Note in the summary that the linked node_modules was replaced
      with a real install so the user knows what changed.

   If the directory is **not** a junction or symlink, treat it as a real test failure
   and continue to step 5.

5. **Summarize the results** in this structure:

   ### Test Results
   - **Status**: PASSED / FAILED / PARTIAL
   - **Total**: X passed, Y failed, Z skipped

   ### Failed Tests (if any)
   For each failure:
   - Test name
   - File and line number
   - Error message (concise)
   - **Likely cause**: Based on recent code changes in this session, what probably
     caused this failure?
   - **Suggested fix**: A concrete next step to resolve it

   ### Passing Notes
   Any notable warnings or deprecations worth flagging even if tests passed.

6. **If all tests pass**, prompt:
   "All tests passing. Run `/updatedocs` to review documentation before committing."

7. **If any tests fail**, prompt:
   "X test(s) failing. Address the failures above and re-run `/test` when ready,
   or run `/updatedocs` if the failures are known/acceptable and you want to proceed."

Always run tests non-interactively (e.g., `--no-watch`, `--ci` flags) so the command
completes and returns output rather than entering watch mode.

Prefer the basic test command (`npm test`, `pytest`) over coverage variants
(`npm run coverage`, `pytest --cov`). Coverage reports add significant output to the
context window without aiding pass/fail diagnosis. Only run the coverage command if
the user explicitly requests it.
