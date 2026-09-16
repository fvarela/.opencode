---
description: Guided walkthrough of recent changes with manual verification steps
---

# Walkthrough Recent Changes

Analyze recent changes and produce a guided walkthrough so the user can understand and manually verify what was done.

## Steps

1. **Gather changes** — Run `git diff HEAD~5..HEAD --stat` and `git log --oneline -10` (adjust range if $ARGUMENTS specifies a commit range, branch, or number of commits). Read the actual diffs for changed files.

2. **Organize by feature** — Group related changes into logical features or units of work.

3. **Prefer VS Code debugger when available** — Check for `.vscode/launch.json` in the workspace root. If a relevant debug configuration exists for the script/service being tested, suggest using it instead of CLI commands. Name the specific launch configuration and mention breakpoints worth setting. Fall back to CLI commands if no config exists.

4. **Write numbered tests** — For each feature/change, write one or more **Test N: Title** sections. Each test must include:
   - A one-line description of what it verifies
   - A **ready-to-copy command block** — full environment variables, inline scripts, one-liners the user can paste directly into the terminal. No placeholders unless truly user-specific (and if so, explain how to get the value).
   - A **"What to check"** bullet list — specific things to look for in the output (field names, expected values, absence of errors, exit codes)
   - Include **negative tests** where relevant (expected errors, missing files, invalid input) — these are just as valuable as happy-path tests

5. **Group by prerequisites** — At the end, add a short summary like: "Tests 1-4 need no external access. Test 5 requires az login and the workspace ID." This lets the user decide how far to go.

## Output format

```
### Test 1: <Title>
<One line explaining what this verifies>

\`\`\`bash
<copy-pasteable command>
\`\`\`

**What to check:**
- <specific observable outcome>
- <specific observable outcome>

---
### Test 2: ...
```

Keep it concise and practical — like a colleague walking through a PR in person. No ceremony.

If $ARGUMENTS is provided, use it to scope the walkthrough (e.g., a branch name, commit range, or task number).
