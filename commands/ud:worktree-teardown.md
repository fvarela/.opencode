---
description: Tear down a worktree — adapts to whether you run it from the worktree or the main checkout
---

# Worktree Teardown

This command does the right thing depending on where it is run. A worktree cannot
remove itself (its own directory is in use by the running session), so teardown is a
two-actor job: the worktree session **finishes the work** (push/PR, or just commit),
and the main-checkout session **cleans up** (merge if needed, remove worktree, delete
branch). Detect the context first, then follow the matching part.

## Step 0: Detect context

```bash
COMMON=$(git rev-parse --git-common-dir)
GITDIR=$(git rev-parse --git-dir)
if [ "$COMMON" != "$GITDIR" ]; then echo "CONTEXT=WORKTREE"; else echo "CONTEXT=MAIN"; fi
git remote get-url origin 2>/dev/null || echo "NO_REMOTE"
```

- `CONTEXT=WORKTREE` → go to **Part A** (finish the work).
- `CONTEXT=MAIN` → go to **Part B** (clean up).
- `NO_REMOTE` (or empty remote) means there is no remote → this changes the flow; note
  it and handle the "no remote" branches below.

---

## Part A — run from INSIDE the worktree (finish the work)

### A1. Commit remaining work
Show `git status`. If there are uncommitted changes, propose a conventional-commit
message and commit them **after** the user confirms. Never commit without showing what
is staged.

### A2a. If a REMOTE exists — detect the host

```bash
git remote get-url origin
```

- URL contains `dev.azure.com` or `visualstudio.com` → **Azure DevOps** (use `az`)
- URL contains `github.com` → **GitHub** (use `gh`)
- Anything else (self-hosted GitLab, Bitbucket, etc.) → **stop and ask the user** how
  they want to open the PR; do not assume.

**Preflight** — make sure the right CLI is installed and authenticated. If the check
fails, tell the user the exact command to fix it and stop:
- Azure DevOps: `az account show` succeeds and the `azure-devops` extension is present.
  Fix: `az login` and/or `az extension add --name azure-devops`.
- GitHub: `gh auth status` succeeds. Fix: `gh auth login`.

Then:
1. Push the branch:
   ```bash
   git push -u origin <current-branch>
   ```
2. **Ask the user:** "Should the PR auto-merge once policies/checks pass, or do you
   want to merge it yourself?"
3. Create the PR (target defaults to the repo's default branch; add squash only if the
   user asks):
   - **Azure DevOps**, auto-merge:
     ```bash
     az repos pr create --auto-complete true --delete-source-branch true --title "<title>" --open
     ```
   - **Azure DevOps**, manual merge:
     ```bash
     az repos pr create --title "<title>" --open
     ```
   - **GitHub**, auto-merge:
     ```bash
     gh pr create --title "<title>" --body "<summary>" --fill
     gh pr merge --auto --delete-branch
     ```
   - **GitHub**, manual merge:
     ```bash
     gh pr create --title "<title>" --body "<summary>" --web
     ```
4. Report the PR URL.
5. Tell the user:
   > PR opened.<if auto-merge: It will complete automatically once policies/checks pass.>
   > When it is merged, switch to your MAIN session and run `/ud:worktree-teardown`
   > there to pull, remove this worktree, and delete the branch.

**Do not** attempt to remove the worktree from here — a worktree cannot remove itself.
The merge happens on the remote; never also merge locally.

### A2b. If NO remote exists
There is no PR path. Ask the user whether they want to set up a remote or just work
locally. If they choose local, tell them:
> Your work is committed on `<current-branch>`. Switch to your MAIN session and run
> `/ud:worktree-teardown` there — it will merge this branch into the main branch
> locally, then remove the worktree.

---

## Part B — run from the MAIN checkout (clean up)

### B1. Pick the worktree
```bash
git worktree list
```
If more than one linked worktree exists, ask the user which one to tear down. Identify
its directory path and branch name.

### B2a. If a remote exists — the merge already happened on the remote
Just sync the main branch:
```bash
git checkout <main-branch>
git pull
```

### B2b. If NO remote exists — merge locally
Confirm the target branch (usually `main` or `master`), then:
```bash
git checkout <main-branch>
git merge <feature-branch>
```
If the merge conflicts, stop and hand control to the user — do not force it.

### B3. Remove the worktree and branch
```bash
git worktree remove ../<repo>-<slug>
git branch -d <feature-branch>
git worktree prune
```
- `git branch -d` (lowercase) is intentional: it refuses to delete an unmerged branch.
  If it errors, the branch was not merged — tell the user and stop. Only use `-D` if
  the user explicitly confirms they want to discard unmerged work.
- If `git worktree remove` reports a dirty or locked worktree, report it and let the
  user decide; do not use `--force` without confirmation.

### B4. Confirm
Show `git worktree list` and the current branch to confirm cleanup is complete.
