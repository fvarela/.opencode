---
description: Initialize or migrate a project for the standard ud: workflow
---

# Initialize or Migrate Project

Set up a project for the standard ud: workflow. Handles both new projects and existing ones.

## CRITICAL RULES

- **NEVER overwrite, delete, or truncate any existing file without explicit user approval.**
- **NEVER run commands that write to files that already exist** (no `echo >`, `cp` over, `>` redirect, etc.).
- **Read files before deciding what to do with them.** Don't assume a file is empty or unimportant.
- When in doubt, ask the user.

## Step 1: Analyze what exists

Run these exact commands using Bash (NOT Glob, NOT Read, NOT any other tool):

```bash
ls -la PROJECT.md AGENTS.md CLAUDE.md 2>&1
```
```bash
ls -la .taskmaster/tasks/tasks.json .taskmaster/docs/ 2>&1
```
```bash
ls -la .claude/ .agents/ 2>&1
```
```bash
ls -la .claude/docs/ 2>&1
```

These will show what exists and what doesn't (missing items show "No such file or directory").

Then:
- If `.taskmaster/tasks/tasks.json` exists, read it with the Read tool and count how many tasks it contains.
- If `.claude/docs/` exists, list its contents — there may be files beyond INSTRUCTIONS.md that need migration.

**STOP HERE. Report your findings to the user in a table. Do NOT proceed to Step 2 until the user acknowledges.**

## Step 2: Create ONLY what is missing

For each item, follow the rule: **if it exists, leave it alone.**

**AGENTS.md** (single source of truth: agent instructions + project planning — Overview,
Architecture, Modules, Development Phases, Decisions, etc.):
- If missing and `CLAUDE.md` exists, read `CLAUDE.md` first — rename it to `AGENTS.md` if it has useful content, or create `AGENTS.md` from `~/.config/opencode/templates/AGENTS.md` and ask the user what to do with `CLAUDE.md`
- If missing and no `CLAUDE.md`, copy from `~/.config/opencode/templates/AGENTS.md`
- **If it exists, leave it alone**

**PROJECT.md** (legacy):
- No longer created for new projects — its content now lives in `AGENTS.md`.
- If a project already has `PROJECT.md`, leave it alone. See Step 3 for the optional consolidation offer.

**.taskmaster/**:
- If `.taskmaster/tasks/tasks.json` already exists, **do nothing** — Taskmaster is already initialized
- If `.taskmaster/` exists but `tasks.json` does not, just create the missing subdirectories (`mkdir -p .taskmaster/tasks .taskmaster/docs`)
- If `.taskmaster/` does not exist at all, run `npx -y task-master-ai init --yes` (the `--yes` flag skips interactive prompts)

## Step 3: Handle legacy files

Only after Step 2 is complete. **Always ask the user before deleting anything.**

**`CLAUDE.md`** (if it still exists after Step 2):
- Read it. Show the user its contents.
- If `AGENTS.md` now exists, ask: "CLAUDE.md still exists. Should I merge its content into AGENTS.md and delete it, or leave it?"

**`PROJECT.md` and `AGENTS.md` both exist** (legacy dual-file setup):
- Leave both files alone by default — do not merge automatically.
- Simply note to the user: "This project has both PROJECT.md and AGENTS.md. Going forward, new projects use a single AGENTS.md that includes the planning sections (Overview, Architecture, Modules, Development Phases, Decisions, etc.). You can keep both as-is, or ask me to merge PROJECT.md's content into AGENTS.md and remove PROJECT.md whenever you're ready."
- Only act on this if the user explicitly asks for the merge.

**`.claude/` directory**:
- If `.claude/docs/` exists, list ALL files in it.
- `.claude/docs/INSTRUCTIONS.md` → merge its content into `AGENTS.md`, then delete the file
- All other files in `.claude/docs/` (INPUT.md, DRAFT.md, etc.) → move to `.agents/docs/` (create it if needed)
- If `.claude/commands/` exists, move to `.agents/commands/`
- Tell the user what you're moving and ask for confirmation before proceeding
- Only delete `.claude/` after the user confirms and it's empty

**`.agents/docs/INSTRUCTIONS.md`** (legacy from intermediate migration):
- If it exists, tell the user: "`AGENTS.md` is now the single instructions file. Want me to merge `.agents/docs/INSTRUCTIONS.md` into it?"

**`project_phase*.md` files**:
- Mention them: "Found legacy phase files. These are now replaced by PRDs in `.taskmaster/docs/`."

**Other legacy files** (`DEVIATIONS.md`, `sync-instructions`, etc.):
- Mention them and suggest deletion, but don't delete without asking

## Step 4: Summary

Show what was created/found:
```
Project structure:
  ✔ AGENTS.md (exists / created / migrated from CLAUDE.md)
  ✔ .taskmaster/ (exists / initialized)
  ✔ .taskmaster/tasks/tasks.json (exists, N tasks / created empty)
  ✘ .taskmaster/docs/ — no PRDs yet

(If PROJECT.md also exists: note it as a legacy file that can optionally be merged into AGENTS.md.)

Next steps:
  1. Fill in AGENTS.md with your project details (Overview, Architecture, Modules, Development Phases)
  2. Run /ud:new-prd to generate a PRD from AGENTS.md
  3. Run /ud:parse-prd to create tasks
  4. Run /ud:next to start working
```

Adjust based on what already exists. Skip steps that are already done.
