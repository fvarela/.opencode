---
description: Initialize or migrate a project for the standard ud: workflow
---

# Initialize or Migrate Project

Set up a project for the standard ud: workflow. Handles both new projects and existing ones.

## Step 1: Analyze what exists

Check for:
- `PROJECT.md` — project context document
- `.claude/docs/INSTRUCTIONS.md` — workflow instructions
- `.taskmaster/` — Task Master directory
- `.taskmaster/tasks/tasks.json` — existing tasks
- `.taskmaster/docs/` — existing PRDs
- `CLAUDE.md` — agent scratchpad
- Legacy files: `project.md`, `project_phase*.md`, `PROJECT.md` in wrong location

Report what you found.

## Step 2: Create missing structure

For each missing item:

**PROJECT.md** (project root):
- If missing, copy from `~/.claude/templates/PROJECT.md`
- If a legacy `project.md` (lowercase) exists, ask user: "Found `project.md`. Should I rename it to `PROJECT.md` and use it as the project context document?"

**.claude/docs/INSTRUCTIONS.md**:
- If missing, copy from `~/.claude/templates/INSTRUCTIONS.md`
- If it exists, leave it alone

**.taskmaster/**:
- If missing, run `task-master init`
- If it exists, leave it alone

**CLAUDE.md** (project root):
- If missing, create it with this content:
  ```
  <!-- Auto-managed scratchpad. For authoritative project context, see .claude/docs/INSTRUCTIONS.md -->
  ```
- If it exists and does NOT have the header, prepend the header line to the top of the file.
- If it exists and has content: scan for high-level project context (tech stack, architecture decisions, conventions, environment details). Extract anything relevant and add it to the Project Context section of `.claude/docs/INSTRUCTIONS.md`, following the same principles as `/ud:save-context` — concise, high-level facts only, no task-specific details or session notes. Ask the user to confirm before writing to INSTRUCTIONS.md.

## Step 3: Handle legacy files

If you find legacy files from older workflows:

**`project_phase*.md` files**:
- Tell the user: "Found legacy phase files. These are now replaced by PRDs in `.taskmaster/docs/`. You can move relevant content to PROJECT.md (for context) or generate new PRDs with `/ud:new-prd`."

**`INPUT.md` in `.claude/docs/`**:
- Leave it alone — `/ud:input` still uses it for ad-hoc work

**`DEVIATIONS.md`, `sync-instructions`, or other old workflow files**:
- Mention them and suggest the user can delete them

## Step 4: Summary

Show what was created/found:
```
Project structure:
  ✔ PROJECT.md (exists / created)
  ✔ .claude/docs/INSTRUCTIONS.md (exists / created)
  ✔ CLAUDE.md (exists / created — header verified)
  ✔ .taskmaster/ (exists / initialized)
  ✘ .taskmaster/docs/ — no PRDs yet

Next steps:
  1. Fill in PROJECT.md with your project details
  2. Run /ud:new-prd to generate a PRD from PROJECT.md
  3. Run /ud:parse-prd to create tasks
  4. Run /ud:next to start working
```

Adjust the next steps based on what already exists. If PROJECT.md is already filled in, skip step 1. If PRDs exist, skip step 2. Etc.
