---
description: Create an isolated git worktree for a parallel session, named after the feature goal
---

# Create Worktree

Create a git worktree so a second opencode session can work in isolation, without
filesystem conflicts against the session running in the main checkout.

Worktrees solve the real problem behind "two sessions fighting over git": two sessions
sharing one working directory clobber each other's uncommitted files. A worktree gives
each session its own directory and branch, backed by the same `.git`.

## Guardrail: only run from the main checkout

Run this first:

```bash
if [ "$(git rev-parse --git-common-dir)" != "$(git rev-parse --git-dir)" ]; then
  echo "INSIDE_WORKTREE"
fi
```

If it prints `INSIDE_WORKTREE`, **stop** and tell the user: worktrees should be created
from the main checkout, not nested inside another worktree. Do not proceed.

## Step 1: Ask for the goal

Ask the user: **"What is this worktree for? Describe the feature or fix in a sentence."**
Wait for their answer. Do not guess a name from thin air — the whole point is that the
user describes the goal in plain language and you turn it into a clean branch name.

## Step 2: Derive a name (do NOT create anything yet)

From the user's description:
- Choose a prefix: `fix/` if it is a bug fix, otherwise `feature/`.
- Derive a short, kebab-case slug (2-4 words) capturing the essence. Examples:
  - "fix the login form that breaks on empty email" → `fix/login-empty-email`
  - "add CSV export to the reports page" → `feature/csv-export`
- Compute the worktree directory as a sibling of the repo, named `<repo>-<slug>` using
  the slug WITHOUT the prefix (so no nested `feature/` folder is created). Get the repo
  directory name with:
  ```bash
  basename "$(git rev-parse --show-toplevel)"
  ```

## Step 3: Confirm before creating

Show the user exactly what will happen and wait for a "yes":

```
Branch:    <prefix>/<slug>
Directory: ../<repo>-<slug>
Command:   git worktree add ../<repo>-<slug> -b <prefix>/<slug>
```

If the user wants a different name or prefix, adjust and re-confirm. Never create the
worktree before the user approves.

## Step 4: Create

```bash
git worktree add ../<repo>-<slug> -b <prefix>/<slug>
```

## Step 5: Hand off

opencode's working directory is fixed when it launches — this command cannot move the
current session into the new worktree. The user must start a fresh session there. Tell
them:

> Worktree ready at `../<repo>-<slug>` on branch `<prefix>/<slug>`.
> Start the second session there:
>
>     cd ../<repo>-<slug> && opencode
>
> Notes:
> - The worktree contains the last committed state. **Uncommitted changes in this
>   session stayed behind** — they are not in the worktree.
> - No Taskmaster tag was created. If that session needs to track tasks, create a tag
>   from inside it then (sessions on different features should use different tags to
>   keep `tasks.json` edits from conflicting).
> - When that session's work is done, run `/ud:worktree-teardown` from inside it.
