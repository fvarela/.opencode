# Global Instructions

These apply to every project. Project-specific context lives in each project's own `AGENTS.md`.

## Working Style
- Keep solutions simple and within the agreed scope. Ask before expanding it.
- Separate what you verified (ran, measured) from what you infer. If you don't
  know, say so; never make up an explanation. Tell me plainly when I or you
  are wrong.
- Change files only in the current repository. Never change cloud access,
  permissions, firewall rules or credentials; ask me.
- Long-running jobs: don't poll or read logs while they run. Either launch and
  return, or wait once for the expected duration and read only the final state.
- I'm in Europe/Paris (CET/CEST).

## Tool Rules
- Don't use Glob to find files. It silently returns nothing for paths that exist, including dotfile folders (`.opencode/`, `.git/`). Use Read on the exact path, or `ls`/`find` if Bash is available.
- If a tool reports "no files found" for a path that should exist, cross-check with another tool before concluding it doesn't exist.

## Git
- Never commit to `main`. Work on a `feature/<name>` or `fix/<name>` branch, one branch per unit of work.
- Conventional commits: `feat:`, `fix:`, `docs:`, `chore:`, `refactor:`, `test:`.
