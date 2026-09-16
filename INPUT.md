Ok, let's go with model A.
push branch, open PR, merge on remote (will be azure devops mostly) and then git pull and delete worktree (if it exists). Never merge locally.
Perhaps for small project when I still don't have remote set up the agent can just merge locally and delete the worktree (if it exists).

1. Who creates the worktree, and when?
   I agree about having a special command /ud:worktree. I can use that command when I know I have another session that is working and I need the isolation for the current session that worktree provides.

2. Where do worktrees live?
   I agree that a sibling directory is a good idea.

3. Taskmaster state (.taskmaster/tasks.json) is shared and per-session
   Not every sessions edit tasks.json so I don't want to force every session to have a PRD. I lean towards treating conflicts on it as "Take both" for being the lightest touch. Making a PRD key with some kind of hash would work too. Thoughts?

4. PROJECT.md. Similar to before I think we should treat the conflicts as "Take both". Note that we may have PM running on different sessions.

5. Which agents does this bind?
   I don't have a preference.

Regarding your questions:

1. Do your projects usually have a remote, usually not, or a mix?
   Usually yes, but sometimes no.
2. Do you want the agent to run git worktree add / teardown itself, or do you prefer to set up the worktree yourself and just tell the agent "you're in a worktree, act accordingly"?
   I want the agent to run it.
3. How do you launch a second session today
   A fresh opencode in the same folder is how I have been doing it.
4. For the tasks.json sharing problem
   Two sessions working on different features. Never on the same one.

With the option 1 I would from the original session run /ud:worktree to create a worktree for the second session. Then I would start the second session in the worktree (therefore I would cd to the worktree and run opencode?). The opencode configuration expects to have some files in the root of the repo. Check the settings. We have the PROJECT.md, the .taskmaster folder, and the .agents folder. Will the worktree have a copy of those files?

Just like we have a /ud:worktree should we have a /ud:worktree-teardown command to remove the worktree when working from the second session?
Regarding your questions:

1. Confirm before creating, or create immediately?
   Confirm first
2. Name format — always feature/?
   Agree with your suggestion: allow fix/ and feature/, keep it to those two.
3. Worktree directory name.
   Agree with your suggestion
4. The tag question (still open from before).
   No, it should not set a taksmaster tag. Remember not all session will generate tasks that we would register using taskmaster. Initiallizing a taskmaster tag would be an overkill for those sessions that don't need it. When I start the second session in the worktree we can generate a taskmaster tag if we need it. I don't think it is necessary to create it in advance before starting the second session.

Just one adaptive /ud:worktree-teardown
Regarding the PR merge I lean towards the agent asking me before it creates the PR if I want it to merge automatically or if I want to merge it myself. Honestly most of the time I would ask it to merge automatically. When working solo (most of the time) I want to avoid the extra step of merging manually. Having it ask me before merging would allow me to adapt in the future in case I want to work with a team and have the agent create the PR but not merge it automatically.

1. The repo's own AGENTS.md
   Update it to match the new template.
2. az repos pr create assumes Azure CLI + the azure-devops extension are installed and authenticated
   That will be the default assumption however I may have some personal projects where I use github instead. If possible I would like to support both.
   Check if that is possible and where should I inform the agent if we are using github or azure devops. I assume the PROJECT.md is the right place to put that information but please check and confirm
