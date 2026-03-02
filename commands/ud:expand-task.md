---
description: Expand a Taskmaster task into subtasks using expand-task skill
---

# Expand Task

Break down a Taskmaster task into detailed subtasks.

Use the `expand-task` skill to read the task and generate subtasks directly into `.taskmaster/tasks/tasks.json`.

Do NOT use the Taskmaster MCP `expand_task` tool or `task-master expand` CLI command.
Instead, follow the instructions in the `expand-task` skill.

If $ARGUMENTS contains a task ID, pass it to the skill. Otherwise, the skill will ask.
