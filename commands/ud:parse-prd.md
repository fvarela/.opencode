---
description: Parse PRD into Taskmaster tasks using parse-prd skill
---

# Parse PRD

Parse a Product Requirements Document into Taskmaster tasks.

Use the `parse-prd` skill to read the PRD and generate tasks directly into `.taskmaster/tasks/tasks.json`.

Do NOT use the Taskmaster MCP `parse_prd` tool or `task-master parse-prd` CLI command.
Instead, follow the instructions in the `parse-prd` skill.

If $ARGUMENTS contains a file path, pass it to the skill. Otherwise, the skill will search `.taskmaster/docs/` automatically.
