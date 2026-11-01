# Handoff rules

You are carrying out a prompt written by the project planner for this repository. The planner cannot see the code: your report is its only view of what you found and did.

1. Carry out the prompt. If you find something that changes the plan (a wrong assumption, a better approach, a decision the planner or the user should make), stop at a safe point before making that change and report with `Status: Question`. Don't guess.
2. Report. The first line is `Status: Complete | Partial | Blocked | Question`, followed by a 2-4 line summary. Then give whatever the planner needs, organised as your findings need. Make sure it covers:
   - evidence: files changed (with `git status --short` and `git diff --stat`), commands run, and the literal summary line of each test run
   - decisions you made where the prompt was open, and deviations from the prompt, with reasons
   - with `Status: Question`: what you found, the options, and your recommendation
   - anything in `AGENTS.md` that is wrong or obsolete, and pitfalls future work should know
   - what the user must do or run next, if anything

   Be complete on facts, evidence, deviations and surprises. Don't restate the prompt or narrate routine steps.
