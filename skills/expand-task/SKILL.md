---
name: expand-task
description: >-
  Break down a Taskmaster task into subtasks. Use when user says "expand task",
  "break down task", "add subtasks", or runs /ud:expand-task. Reads the task from
  .taskmaster/tasks/tasks.json and adds detailed subtasks.
  This skill replaces Taskmaster's built-in expand_task tool — no API key needed.
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
---

# Expand Taskmaster Task into Subtasks

You are breaking down a Taskmaster task into detailed, actionable subtasks.
This skill replaces Taskmaster's built-in `expand_task` MCP tool, using the host agent's
intelligence instead of a separate API call.

## Step 1: Identify the task

The task ID comes from:
1. `$ARGUMENTS` — e.g., "5" or "task 5"
2. If not provided, ask the user which task to expand

## Step 2: Read tasks.json

First, confirm you are in the correct project directory:
```bash
pwd && ls .taskmaster/tasks/tasks.json 2>&1
```

If the file is not found, you may be in the wrong directory. Use the project root as the working directory.

Read `.taskmaster/tasks/tasks.json` and find the task by ID.

If the task already has subtasks:
- Ask the user: "Task <id> already has <n> subtasks. Do you want to replace them or add more?"
- If replacing, clear the existing subtasks array
- If adding, note the highest subtask ID to continue numbering

## Step 3: Understand context

Read the task's `title`, `description`, `details`, and `testStrategy`.
Also check the project's:
- `AGENTS.md` — for project conventions, patterns, and context
- The PRD in `.taskmaster/docs/` — for broader requirements context

This project context is an advantage over Taskmaster's built-in expand_task,
which has no access to the broader project.

## Step 4: Generate subtasks

Break the task into 3-7 subtasks (unless the user specifies a different number).

Each subtask follows this format:

```json
{
  "id": <integer>,
  "title": "<concise action title>",
  "description": "<what this subtask accomplishes>",
  "status": "pending",
  "dependencies": [<array of subtask IDs within this task>],
  "details": "<specific implementation steps, code patterns, files to touch>"
}
```

## Subtask Generation Rules

1. **IDs are sequential integers** starting from 1 within the parent task. They are referenced as `<parentId>.<subtaskId>` (e.g., task 5, subtask 2 = "5.2").
2. **Dependencies** reference other subtask IDs within the same parent (just the subtask number, not the full dot notation). Use an empty array `[]` if no internal dependencies.
3. **Ordering**: Subtasks should follow a logical implementation order.
4. **Granularity**: Each subtask should be completable in 15-60 minutes. Smaller is better.
5. **First subtask**: Often a setup/scaffolding step (create file, set up test structure).
6. **Last subtask**: Often integration testing or verification.
7. **Details**: Be very specific — include file paths, function names, patterns to follow, commands to run.

## Step 5: Write back to tasks.json

Update the task's `subtasks` array in `.taskmaster/tasks/tasks.json`.
Do NOT modify any other tasks or fields.

## Step 6: Summary

Display:
- Parent task title and ID
- Number of subtasks created
- The subtask list with titles
- Suggested first subtask to start with

Do NOT use the Taskmaster MCP `expand_task` tool. Write the file directly.
