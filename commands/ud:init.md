---
description: Initialize a new project for the Task Master workflow
---

# Initialize New Project

Walk the user through setting up a new project for the Task Master workflow:

1. Create `.claude/docs/` directory
2. Copy `~/.claude/templates/INSTRUCTIONS.md` to `.claude/docs/INSTRUCTIONS.md`
3. Remind user to fill in the **Project Context** section
4. Run `task-master init` if not already initialized
5. Suggest next steps: write or generate a PRD (`/ud:new-prd`), then parse it (`/ud:parse-prd`)
