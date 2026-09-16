# [Project Name]

This project follows the standard `ud:` command workflow. Run `/ud:next` to start.

## Overview

### What We're Building
<!-- One paragraph: what is this project? -->

### The Problem
<!-- What problem does it solve? Why does it matter? -->

### The Solution
<!-- High-level approach. How does this project solve the problem? -->

## Ongoing Topics

<!-- Active discussion threads from planning sessions.
     The planner agent maintains this list:

     HOW IT WORKS:
     - When entering planner mode, CHECK THIS SECTION FIRST for open threads
     - During conversation, EXTRACT new topics as they emerge and add them here
     - Each topic gets a short title and a summary of the current thinking
     - When a conversation touches multiple topics, make sure ALL of them are
       captured here — don't let topics get lost in the flow
     - Once a topic is resolved, move conclusions to the appropriate section
       (Decisions, Design Principles, Architecture, etc.) and remove it from here

     FORMAT:
     - [ ] **Topic Title** — Current state of thinking / open questions
     - [x] ~~**Resolved Topic**~~ — Moved to [Section Name]

     EXAMPLE:
     - [ ] **Query routing strategy** — Deciding between ML-based vs rule-based. Leaning toward hybrid: hard rules first, ML fallback. Open: what exactly are the "hard rules"?
     - [ ] **Collection persistence model** — Collections are groups of queries in DB. Frontend edits are temporary until explicit save. Open: optimistic vs pessimistic save UX?
     - [x] ~~**Auth approach**~~ — Decided on Azure AD SSO. See Decisions section.
-->

## Architecture

### Components
<!-- List the main components/services and how they interact.
     Example:
     - **API Server**: Express.js REST API handling client requests
     - **Worker**: Background job processor for async operations
     - **Database**: PostgreSQL with Prisma ORM
-->

### Infrastructure
<!-- Where does this run? Cloud provider, deployment model, environments.
     Example:
     - Azure Container Apps (dev, staging, prod)
     - Azure Blob Storage for file processing
     - MongoDB Atlas for document storage
-->

## Modules

<!-- List each module/service in the project. Update Status as work progresses. -->

### module-name
**Purpose:** What this module does
**Status:** Planning | In Progress | Complete
**Key Requirements:**
- Requirement 1
- Requirement 2
**Tech Stack:** Language, frameworks, key libraries

## Development Phases

<!-- Ordered list of phases. /ud:new-prd reads this to determine what to generate next.
     Mark phases as they progress. Each phase becomes a PRD in .taskmaster/docs/.

     Example:
     - [x] Phase 1: Project scaffolding and CI/CD setup
     - [x] Phase 2: Core data model and storage layer
     - [ ] Phase 3: API endpoints and authentication
     - [ ] Phase 4: Background job processing
     - [ ] Phase 5: Monitoring and alerting
-->

## Pending Fixes

<!-- Small tasks, bugs, and housekeeping items that don't fit in a development phase.
     Add items as you notice them. When starting a new phase, review this list and
     fold relevant items into that phase's PRD, or handle them as quick standalone tasks.
     Check off items when done.

     Example:
     - [ ] Fix typo in API error message for /health endpoint
     - [ ] Update module status in this file to reflect current state
     - [ ] Delete outdated docs/OLD_API.md
     - [x] Add missing index on users.email column
-->

## Design Principles

<!-- Architectural and coding principles for this project.
     Example:
     - **Idempotent Processing**: All jobs can be safely re-run without side effects
     - **Configuration via Environment**: No hardcoded secrets or environment-specific values
-->

## Decisions

<!-- Log key architectural and technical decisions with dates and rationale.
     Example:
     - **2026-01-15:** Chose MongoDB over PostgreSQL for document flexibility
     - **2026-02-01:** Switched from REST to gRPC for inter-service communication
-->

## Tool Rules
- **NEVER use Glob to find files in dotfile directories** (`.taskmaster/`, `.agents/`, `.claude/`, etc.). Glob silently skips dotfile-prefixed paths. Use `ls` via Bash or `Read` on the directory instead.
- When a tool returns "no files found" for a path that should exist, **cross-check with a different tool** before concluding the files don't exist.

## Project Context
<!-- Project-specific details: conventions, patterns, gotchas, environment setup.
     This is NOT for architecture (that's in the Architecture/Modules sections above) or
     tasks (that's in .taskmaster/). This is for things the agent needs to know that
     aren't obvious from the code.
     Examples:
     - "We use Azure CLI for deployments, never Terraform"
     - "All API responses must include a correlation ID header"
     - "Tests run against a local Docker MongoDB, start with: docker compose up -d"
-->

## Taskmaster Setup
This project uses Taskmaster MCP in **core-only mode** (no API key).
AI-powered Taskmaster features are handled by custom skills instead:
- `/ud:parse-prd` — parses PRDs into tasks (uses `parse-prd` skill, NOT the Taskmaster MCP `parse_prd` tool)
- `/ud:expand-task` — expands tasks into subtasks (uses `expand-task` skill, NOT the Taskmaster MCP `expand_task` tool)
- `/ud:new-prd` — generates PRDs from this file's Development Phases section (uses `generate-prd` skill)

Taskmaster core tools (get_tasks, next_task, set_task_status, etc.) work normally.

## Completing a Task
1. Check for deviations — if implementation diverged from the task, explain why and update the task details directly in `.taskmaster/tasks/tasks.json`
2. Mark done — use the `set_task_status` tool with status `done`
3. Summarize — run `/ud:summarize` (or write a `##### Task <id>: <title>` + 2-3 sentence summary)
4. Commit — ask before committing. Conventional commits: `feat:`, `fix:`, `docs:`, `chore:`, `refactor:`, `test:`. Ask before pushing.

## Git Conventions

- **`main`** is stable — never commit to it directly. All code changes happen on a
  `feature/<name>` or `fix/<name>` branch.
- **One branch per unit of work.** Commit after each task (conventional commits:
  `feat:`, `fix:`, `docs:`, `chore:`, `refactor:`, `test:`).

### Parallel sessions — use worktrees

When more than one opencode session works on this repo at the same time, they must not
share the same working directory — they will clobber each other's uncommitted files.
Isolate each parallel stream in its own git worktree:

1. From the main checkout, run `/ud:worktree`. It asks what the work is for, derives a
   branch name, and creates a sibling worktree at `../<repo>-<name>`.
2. Start the second session there: `cd ../<repo>-<name> && opencode`.
3. Do the work in that session, committing to its branch.

### Finishing a worktree — `/ud:worktree-teardown`

Run `/ud:worktree-teardown`; it adapts to where you run it (it detects worktree vs.
main checkout, and the git host from the remote URL).

- **With a remote (Azure DevOps or GitHub):** from the worktree session it commits,
  pushes, and opens a PR — asking whether to auto-merge once checks pass or let you
  merge it yourself. The PR is merged **on the remote**, never locally. Afterwards, from
  the main session, teardown pulls the main branch, removes the worktree, and deletes
  the branch.
- **Without a remote:** the worktree session just commits. From the main session,
  teardown merges the branch into the main branch locally, then removes the worktree
  and branch.

**Never merge both locally and on the remote** — pick the path that matches your setup.

### Shared files across worktrees

`tasks.json` and `AGENTS.md` are shared and may conflict if two sessions edit them:
- Sessions working on different features should use **different Taskmaster tags** so
  their `tasks.json` edits touch different regions. Do **not** try to "union" / "take
  both" a `tasks.json` conflict — it is a single JSON document and that corrupts it.
  Resolve any conflict by hand.
- `AGENTS.md` conflicts are prose — resolve by keeping both blocks.
