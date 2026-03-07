---
description: Find and start the next Taskmaster task, handles tag switching
---

# Next Task

Check if `.claude/docs/INSTRUCTIONS.md` exists using `ls .claude/docs/INSTRUCTIONS.md` (never use Glob for this).
If it does not exist, warn the user and suggest running `/ud:init`.

## Steps

1. Check the current Taskmaster tag by reading `.taskmaster/state.json` — look at the `currentTag` field.
2. Use Taskmaster's `next_task` tool to get the next available task.
3. If `next_task` returns no eligible tasks:
   - List available tags with `task-master tags`
   - Check if there's a more recent tag with pending tasks
   - Ask the user: "No tasks available in the current tag (**[tag]**). Available tags: [list]. Which tag should I switch to?"
   - Switch with `task-master use-tag <tag>` and retry `next_task`
4. If the task has **no subtasks**, suggest expanding it first:
   > "This task has no subtasks. I recommend running `/ud:expand-task <id>` to break it into steps before starting. This ensures progress is tracked across sessions. Want me to expand it now?"
   If the user agrees, run the expand-task skill. If not, proceed without subtasks.
5. Set the task status to `in-progress`.

## Behavior by Role
Check the default role in `.claude/docs/INSTRUCTIONS.md`:
- **Assisted**: Present the task, outline the approach, wait for confirmation before proceeding
- **Agentic**: Explain the task briefly, then begin implementation directly
