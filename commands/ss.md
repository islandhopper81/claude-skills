# /ss — Screenshot Visual Input

Arguments: $ARGUMENTS

## Instructions

The screenshots folder is: `C:\Users\scott\OneDrive\Pictures\Screenshots`

### Step 1 — Parse Arguments

The arguments string may look like any of these:
- (empty) → count=1, action=""
- `huh` → count=1, action="huh"
- `fix` → count=1, action="fix"
- `4` → count=4, action=""
- `3 make infographic plz` → count=3, action="make infographic plz"
- `do this` → count=1, action="do this"

Parse rules:
1. If the first token is a positive integer, treat it as the screenshot count. Everything after it is the action string.
2. If the first token is NOT a number, the count is 1 and the entire arguments string is the action.
3. If arguments are empty, count=1 and action="" (just show and explain the screenshot).

### Step 2 — List and Load Screenshots

Use PowerShell to list the screenshots folder sorted newest-first, then take the top N files matching the resolved count:

```powershell
Get-ChildItem "C:\Users\scott\OneDrive\Pictures\Screenshots" -File |
  Sort-Object LastWriteTime -Descending |
  Select-Object -First <count> -ExpandProperty FullName
```

Read each returned file path using the Read tool (which supports images). Do this before responding.

### Step 3 — Execute the Action

Based on the action string, behave as follows:

#### No action / blank / "huh"
Describe exactly what you see in each screenshot. Be specific: UI state, error messages, code visible, layout, data. If multiple screenshots, describe each then note what they share.

#### "fix"
Treat the screenshot as showing a problem — either:
- **Code error / stack trace / console output**: Identify the bug, find the relevant source file(s) in the current working directory, and edit the code to fix it. Explain what caused it.
- **UI visual bug** (overlapping elements, broken layout, wrong styling, etc.): Find the component responsible and fix the styling/logic. Explain what was wrong.
Use judgment on which case applies based on what's visible.

#### "do this" / "do this for me" / similar
The user screenshotted something impressive or useful they saw online or elsewhere. Analyze what makes it work, then implement or adapt the best version of it for this project — remixing it toward the user's specific goals based on what you know about them and the current codebase. Explain what you're building and why it fits.

#### Anything else (free-form instruction)
Treat the action string as a direct instruction and execute it using the screenshot content as your primary input. Examples:
- "make infographic plz" → synthesize the screenshot content into a clean infographic (as an artifact or code)
- "write a ticket for this" → draft a Jira ticket based on what's visible
- "what library is this" → identify the framework/library shown
- "add this to our app" → implement what's visible into the current codebase

### Notes
- Always read the actual image files — never guess at content.
- If a file path has spaces, quote it when passing to tools.
- Newest = highest `LastWriteTime`. Use that order consistently.
- If the screenshots folder is empty or fewer files exist than requested, load what's available and note it.
