# /test — Run Tests and Summarize Results

Run the project test suite and provide an actionable summary.

## Instructions

1. **Read test commands** from `CLAUDE.md`. Look for a section defining unit test and
   integration test commands (e.g., `npm run test`, `pytest`, `nextflow run tests/`).
   If not defined in `CLAUDE.md`, inspect `package.json`, `Makefile`, or `pyproject.toml`
   to infer the correct test command before asking the user.

2. **Ensure the test environment is set up.**

   For Python projects (anything using `pytest`, `nextflow`, etc.), the test command
   must run inside the project virtualenv so backend dependencies resolve. Before running:

   - If `$VIRTUAL_ENV` is set, the venv is already active — proceed.
   - If `$VIRTUAL_ENV` is unset and a `.venv/` directory exists at the project root,
     activate it first:
     - Windows PowerShell: `.\.venv\Scripts\Activate.ps1`
     - POSIX shells: `source .venv/bin/activate`
   - If no `.venv/` exists, run the test command as-is. If it then fails with
     `ModuleNotFoundError` for a project dependency (e.g. `No module named 'boto3'`),
     report the missing-venv diagnosis instead of treating it as a real test failure.

3. **Run the tests** and capture full output.

4. **Summarize the results** in this structure:

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

5. **If all tests pass**, prompt:
   "All tests passing. Run `/updatedocs` to review documentation before committing."

6. **If any tests fail**, prompt:
   "X test(s) failing. Address the failures above and re-run `/test` when ready,
   or run `/updatedocs` if the failures are known/acceptable and you want to proceed."

Always run tests non-interactively (e.g., `--no-watch`, `--ci` flags) so the command
completes and returns output rather than entering watch mode.
