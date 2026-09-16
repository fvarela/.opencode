---
name: parse-prd
description: >-
  Parse a PRD document into Taskmaster tasks.json format. Use when user says "parse PRD",
  "parse prd", "generate tasks from PRD", or runs /ud:parse-prd. Reads the PRD file from
  .taskmaster/docs/ and writes structured tasks to .taskmaster/tasks/tasks.json.
  This skill replaces Taskmaster's built-in parse_prd tool — no API key needed.
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
---

# Parse PRD into Taskmaster Tasks

You are converting a PRD document into Taskmaster's `tasks.json` format.
This skill replaces Taskmaster's built-in `parse_prd` MCP tool, using the host agent's
intelligence instead of a separate API call.

## Step 1: Find the PRD

First, confirm you are in the correct project directory by running:
```bash
pwd && ls -la .taskmaster/docs/ 2>&1
```

If `.taskmaster/docs/` is not found, you may be in the wrong directory. Use the project root as the working directory.

Then find the PRD:
1. `$ARGUMENTS` — if a file path is provided, use it
2. If `.taskmaster/docs/` exists, list its contents and pick the most recent PRD (the one for the current/next phase)
3. If multiple PRDs exist, show the list and ask the user which one to parse

If no PRD is found, tell the user and stop.

## Step 2: Read existing tasks.json

Check if `.taskmaster/tasks/tasks.json` already exists.
- If it exists, read it and note the highest task ID. New tasks will start after that ID.
- If it doesn't exist, start from ID 1.

Also check if `.taskmaster/tasks/` directory exists. If not, create it.

## Step 3: Parse the PRD into tasks

Read the PRD carefully. Extract tasks based on:
- Requirements sections (Must Have, Should Have, etc.)
- Implementation phases
- Explicit task breakdowns (if the PRD has a "Task Breakdown" section, use it directly)
- Technical requirements that imply work items

For each task, generate:

```json
{
  "id": <integer>,
  "title": "<concise title>",
  "description": "<1-2 sentence description of what this task accomplishes>",
  "status": "pending",
  "dependencies": [<array of task IDs this depends on>],
  "priority": "<high|medium|low>",
  "details": "<detailed implementation notes, technical approach, specific files to modify>",
  "testStrategy": "<how to verify this task is complete>",
  "subtasks": []
}
```

## Task Generation Rules

1. **Sequential IDs**: Tasks get sequential integer IDs starting from 1 (or next available).
2. **Dependencies**: Express as an array of integer task IDs. A task cannot depend on itself or create circular dependencies. Tasks with no dependencies get an empty array `[]`.
3. **Priority**: Use `high` for blocking/foundational work, `medium` for core features, `low` for nice-to-haves and polish.
4. **Ordering**: Tasks should be in a logical implementation order. Foundation/setup tasks first, then core features, then integration, then polish.
5. **Granularity**: Each task should be completable in 1-4 hours. If a task would take longer, it should be broken into separate tasks or left for `/ud:expand-task`.
6. **Details field**: Include specific files, functions, patterns, or commands. This is what the agent will read when implementing.
7. **Test strategy**: Be concrete — "run the test suite" is too vague. Say "run `pytest tests/test_parser.py` and verify all tests pass" or "trigger the job and confirm logs show SERIAL_RANGE=2470".

## Step 4: Write tasks.json

The full file format is:

```json
{
  "tasks": [
    { ...task1... },
    { ...task2... }
  ]
}
```

If appending to existing tasks, merge the new tasks array with the existing one.

Write to `.taskmaster/tasks/tasks.json`.

## Step 5: Summary

After writing, display:
- Total number of tasks created
- The critical path (chain of dependencies for the highest-priority tasks)
- Any tasks that might benefit from expansion with `/ud:expand-task`

Do NOT use the Taskmaster MCP `parse_prd` tool. Write the file directly.
