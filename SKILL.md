---
name: git-publish
description: Publish the current project to a GitHub remote repository. Use this skill whenever the user wants to push code to GitHub, publish to a remote, upload a project to GitHub, set up remote tracking, or says things like "push to github", "publish this repo", "上传到GitHub", "推送到GitHub". This skill handles git initialization, remote setup, auto-commit of uncommitted changes, and pushing.
---

# Git Publish

Push the current project to a GitHub remote repository with a single workflow.

## Workflow

Follow these steps in order:

### Step 1: Get the remote URL from the user

Before doing anything else, ask the user to provide the GitHub repository URL. Accept formats like:
- `https://github.com/user/repo.git`
- `git@github.com:user/repo.git`
- `https://github.com/user/repo`

If the user hasn't provided a URL yet, ask: "请提供GitHub仓库地址（例如 https://github.com/user/repo.git）："

### Step 2: Check and initialize Git if needed

Run `git status` to check if the current directory is a Git repository.

**If NOT a Git repository:**
1. Run `git init`
2. Run `git add .` to stage all files
3. Run `git commit -m "Initial commit"`
4. Run `git remote add origin <URL>` to add the remote
5. Run `git branch -M <current-branch>` to rename to the detected branch name

**If already a Git repository:**
1. Check if a remote named `origin` already exists:
   - Run `git remote get-url origin`
   - If the URL differs from what the user provided, ask the user whether to update it
   - If no remote `origin` exists, run `git remote add origin <URL>`

### Step 3: Handle uncommitted changes

Check for uncommitted changes with `git status --porcelain`.

**If there are uncommitted changes:**
1. Run `git diff --cached` to see staged changes
2. Run `git diff` to see unstaged changes
3. Stage all changes: `git add .`
4. Analyze the staged changes and generate a commit message by summarizing the diff. The commit message should:
   - Follow conventional commit format: `type(scope): brief summary`
   - Types: `feat` (new feature), `fix` (bug fix), `chore` (maintenance), `docs`, `refactor`, `style`, `test`
   - Title no more than 50 characters
   - Body lists the affected files and what changed
5. Run `git commit -m "<generated message>"`

**If there are no uncommitted changes:**
Proceed directly to push.

### Step 4: Push to GitHub

1. Detect the current branch: `git branch --show-current`
2. Try normal push first: `git push -u origin <branch>`
3. If the push fails (e.g., due to divergent histories), present the error to the user and ask: "普通推送失败，是否使用强制推送？这可能会覆盖远程仓库的历史。(y/n)"
   - If user agrees: `git push -u origin <branch> --force`
   - If user declines: stop and report the situation

### Step 5: Confirm success

After a successful push, report:
- The remote URL
- The branch that was pushed
- The commit message(s) that were pushed
