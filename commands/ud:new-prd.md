---
description: Generate a PRD from PROJECT.md using the generate-prd skill
---

# Generate PRD

Use the **generate-prd** skill to create a Product Requirements Document from PROJECT.md.

The skill reads PROJECT.md for project context, checks existing PRDs in `.taskmaster/docs/`
to determine what's already covered, and generates a focused PRD for the next phase.

Do NOT use the prd-taskmaster skill, INPUT.md, or $ARGUMENTS.
PROJECT.md is the single source of truth for PRD generation.
