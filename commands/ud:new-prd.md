---
description: Generate a PRD from AGENTS.md using the generate-prd skill
---

# Generate PRD

Use the **generate-prd** skill to create a Product Requirements Document from AGENTS.md
(its Development Phases and planning sections).

The skill reads AGENTS.md for project context, checks existing PRDs in `.taskmaster/docs/`
to determine what's already covered, and generates a focused PRD for the next phase.

For legacy projects that still keep planning content in a separate `PROJECT.md`, the
skill falls back to reading that file if AGENTS.md has no planning sections.

Do NOT use the prd-taskmaster skill, INPUT.md, or $ARGUMENTS.
AGENTS.md is the single source of truth for PRD generation.
