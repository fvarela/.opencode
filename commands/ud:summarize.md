---
description: Summarize the task just completed into concise markdown notes
---

# Summarize Completed Task

Review the conversation history and summarize the task that was just performed.

Output format (markdown):

##### Task: <concise title describing what was done>
<2-3 sentences describing what was implemented, key decisions made, and the outcome. Include specific details like directories, tools, endpoints, or commands used.>

Guidelines:
- The title should be action-oriented (e.g., "Create FastAPI Routing Service", "Add Authentication Middleware")
- The summary should capture WHAT was done, HOW (key tools/frameworks), and the RESULT
- Keep it factual and concise — this is for note-taking, not documentation
- If $ARGUMENTS is provided, use it as additional context (e.g., a task number to prepend like "Task 3: ")
- Output ONLY the markdown summary, nothing else
