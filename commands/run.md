---
description: Run the planner's prompt from .agents/plan/prompt.md and write the report
agent: build
---
Follow the handoff rules in @~/.config/opencode/prompts/handoff.md.

1. Delete `.agents/plan/report.md` if it exists.
2. Read the prompt in `.agents/plan/prompt.md`. If it is missing or empty, stop and say so.
3. Check its routing line. If it names a different repository than this one, stop, change nothing, and reply that it was misrouted. A worktree of this repository counts as this repository.
4. Carry it out, following the handoff rules.
5. Write the report to `.agents/plan/report.md`. Then print only its status line and summary; the full report stays in the file for the planner.

Additional instructions from the user, if any: $ARGUMENTS
