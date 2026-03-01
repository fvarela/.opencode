---
description: Find and start the next task using Task Master
---

# Next Task

If `.claude/docs/INSTRUCTIONS.md` does not exist, warn the user and suggest running `/ud:init` instead of proceeding.

Determine the next task to work on using Task Master.

## Steps

1. Run `task-master next` to identify the next task (respects dependencies and priorities)
2. Read the full task details
3. Briefly explain what the task involves and your planned approach

## Behavior by Role

- **Assisted mode**: Explain the task, outline the approach, show the commands/code needed, and wait for the user to confirm before proceeding at each step
- **Agentic mode**: Explain the task briefly, then begin implementation directly
