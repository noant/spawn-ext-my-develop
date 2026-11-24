---
name: commit-push
description: Commit and push changes in a git session repo - stage all, concise conventional message, push to origin. Use for "commit and push".
---

# Commit and push

Part of the spawn-my-develop methodology (repo session: git_create_session(repository: spawn-my-develop)).

1. `git status --short`; `git branch --show-current`.
   - If unrelated files present in the tree, ask whether to include them;
     otherwise commit and push immediately.
2. `git add -A && git commit -m "<message>"` - conventional style (`feat:`, `fix:`, `chore:`, `refactor:`), lowercase, English.
3. `git push origin {branch}`; report commit hash and push result.
   - Always push right after committing; never leave commits unpushed.
