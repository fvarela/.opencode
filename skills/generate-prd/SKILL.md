---
name: generate-prd
description: >-
  Generate a Product Requirements Document from AGENTS.md. Use when user says
  "new PRD", "generate PRD", or runs /ud:new-prd. Reads AGENTS.md (its planning
  sections: Overview, Architecture, Modules, Development Phases, Decisions) for
  project context, checks existing PRDs in .taskmaster/docs/ to determine the
  next phase, and generates a focused PRD ready for /ud:parse-prd. Falls back to
  a legacy PROJECT.md if the project still keeps planning content there. No API
  key needed.
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - AskUserQuestion
---

# Generate PRD from AGENTS.md

You are generating a Product Requirements Document for the next phase/feature of a project.
This skill replaces the external prd-taskmaster skill with a simpler, AGENTS.md-driven approach.

## Step 1: Read AGENTS.md

First, confirm you are in the correct project directory:
```bash
pwd && ls AGENTS.md PROJECT.md .taskmaster/docs/ 2>&1
```

Determine the planning source:
- If `AGENTS.md` exists and contains a **Development Phases** section (or other planning
  sections like Overview/Architecture/Modules), use `AGENTS.md` as the planning source.
- If `AGENTS.md` lacks planning sections but a legacy `PROJECT.md` exists, use `PROJECT.md`
  as the planning source instead (older project layout, kept as-is until the user asks to
  consolidate it into AGENTS.md).
- If neither has planning content, tell the user:
  > "No planning content found in AGENTS.md (or PROJECT.md). Run `/ud:init` to set up the
  > project structure, then fill in AGENTS.md's Overview/Architecture/Development Phases
  > sections before generating a PRD."
  Stop here.

The rest of this skill refers to "the planning source" — substitute AGENTS.md or PROJECT.md
depending on what you determined above.

## Step 2: Identify the next phase

Look for the **Development Phases** section in the planning source. It contains a checkbox list like:

```
- [x] Phase 1: Project scaffolding and CI/CD setup
- [x] Phase 2: Core data model and storage layer
- [ ] Phase 3: API endpoints and authentication
- [ ] Phase 4: Background job processing
```

Find the **first unchecked phase** — that's the next one to generate a PRD for.

If the Development Phases section is missing or empty:
- Check `.taskmaster/docs/` for existing PRD files
- Check `.taskmaster/tasks/tasks.json` for existing tasks and their statuses
- Based on the modules and their statuses in the planning source, infer what the next logical work is
- Present your assessment and ask the user to confirm

If a Development Phases section exists and has a clear next phase, present it:
> "The next phase is: **Phase 3: API endpoints and authentication**.
> Should I generate a PRD for this, or do you want a different one?"

Wait for confirmation before generating.

## Step 3: Gather context for the PRD

From the planning source, read and use:
- **Overview** — to understand the project's purpose and frame the PRD
- **Architecture** — to reference components, infrastructure, and how things connect
- **Modules** — to identify which modules are involved in this phase and their tech stack
- **Pending Fixes** — check for unchecked items that are relevant to this phase. Include them in the PRD requirements. Mention to the user which pending fixes will be folded in.
- **Design Principles** — to ensure the PRD aligns with project principles
- **Decisions** — to reference relevant past decisions

If the planning source is `PROJECT.md`, also check `AGENTS.md` for project-specific conventions
(tool rules, git conventions, etc.) — these live there even in the legacy layout.

## Step 4: Generate the PRD

Write a PRD with this structure:

```markdown
# PRD: [Phase Name from Development Phases]

**Author:** Developer
**Date:** [today's date]
**Status:** Draft
**Source:** AGENTS.md — [Phase reference]  <!-- or PROJECT.md, if that's the planning source -->

---

## Summary

[2-3 sentences: what this phase accomplishes and why it matters]

## Problem Statement

[What problem does this phase solve? What's the current state?]

## Goals

- [Concrete, measurable goal 1]
- [Concrete, measurable goal 2]
- ...

## Requirements

### Must Have

1. **[REQ-001]**: [Requirement title]
   - Description: [what it does]
   - Acceptance: [how to verify it's done]

2. **[REQ-002]**: ...

### Should Have

[Lower priority requirements]

### Out of Scope

[Explicitly list what this phase does NOT cover — reference future phases]

## Technical Notes

[Architecture decisions, file locations, patterns to follow, dependencies.
Pull from the planning source's Architecture, Modules, and Design Principles sections.
Reference conventions from AGENTS.md if available.]

## Task Breakdown Hints

[Suggest a rough ordering of work. This helps /ud:parse-prd generate better tasks.]

1. [First thing to build] - depends on nothing
2. [Second thing] - depends on 1
3. ...
```

## Step 5: Write the PRD

Save to `.taskmaster/docs/<phase-name>-requirements.md`.

Derive the filename from the phase name in kebab-case.
Example: "Phase 3: API endpoints and authentication" → `phase3-api-endpoints-auth-requirements.md`

If `.taskmaster/docs/` doesn't exist, create it.

## Step 6: Next steps

Tell the user:
> "PRD saved to `.taskmaster/docs/<filename>`.
> Review it, then run `/ud:parse-prd` to generate tasks.
> After completing this phase, mark it done in the planning source's Development Phases section."

## PRD Quality Rules

- **Be specific**: "Implement OAuth2 with GitHub provider" not "Add authentication"
- **Reference the planning source**: Pull tech stack, architecture, conventions, testing strategy directly
- **Size appropriately**: A PRD should generate 5-15 tasks. If it would generate more, suggest splitting the phase in the planning source.
- **Include acceptance criteria**: Every requirement needs a concrete "how to verify" statement
- **Stay in scope**: Only cover what the phase describes. Reference future phases in Out of Scope.
- **Use the project's language**: If the planning source says "MongoDB", don't say "database". Be specific.
