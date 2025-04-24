## Appendix B: Useful Git Commands Reference

This appendix provides a quick reference for the Git commands discussed in this textbook, categorized for easier lookup.

**Getting Started & Configuration**

* git --version Show the installed Git version.
* git config --global [user.name](http://user.name) "Your Name" Set the name to be attached to your commits globally.
* git config --global [user.email](http://user.email) "[your.email@example.com](mailto:your.email@example.com)" Set the email to be attached to your commits globally.
* git config --global --list List all global Git configurations.
* git init Create a new, empty Git repository in the current directory.
* git clone <repository-url> Download an existing repository, including its full history, and set up a remote named `origin`.

**Basic Workflow: Status, Staging, Committing**

* git status Show the status of the working directory and staging area (untracked, modified, staged files, current branch).
* git add <file-path> Add changes from the working directory to the staging area for a specific file.
* git add . Add changes from the working directory to the staging area for all files in the current directory and subdirectories (including new files).
* git add -u Add changes for all *tracked* files (modifications and deletions) in the current directory and subdirectories to the staging area.
* git add -A Add all changes (new, modified, deleted) including untracked files in the current directory and subdirectories to the staging area. Equivalent to `git add .` followed by `git add -u`.
* git commit -m "Concise subject line" Create a new commit with currently staged changes and a short message.
* git commit Create a new commit with currently staged changes, opening a text editor for the commit message.
* git commit --amend Amend the most recent commit (change message or add staged changes). **Caution: Rewrites history.**
* git diff Show changes in the working directory not yet staged.
* git diff --staged Show changes in the staging area not yet committed.

**Branching & Merging**

* git branch List local branches. Add `-a` to see remote-tracking branches.
* git branch <new-branch-name> Create a new branch pointing to the current commit, but stay on the current branch.
* git switch <existing-branch-name> Switch to an existing branch.
* git checkout <existing-branch-name> Older command to switch to an existing branch (also used for restoring files).
* git switch -c <new-branch-name> Create a new branch and immediately switch to it.
* git checkout -b <new-branch-name> Older command to create a new branch and immediately switch to it.
* git branch -m <old-name> <new-name> Rename a local branch.
* git branch -d <branch-name> Delete a local branch (only if merged).
* git branch -D <branch-name> Force delete a local branch (even if not merged). **Caution: May lose commits.**
* git merge <branch-to-merge> Merge the specified branch into the current branch. May result in a fast-forward or three-way merge.
* git merge --abort Abort a merge process if conflicts occur.
* git rebase <base-branch> Apply commits from the current branch onto the tip of the base branch. **Caution: Rewrites history.**
* git rebase --abort Abort a rebase process.
* git rebase --continue Continue a rebase process after resolving conflicts.

**Working with Remotes**

* git remote -v List remotes and their URLs.
* git remote add <name> <url> Add a new remote repository with a specified name (e.g., `origin`, `upstream`) and URL.
* git fetch <remote-name> Download commits, files, and refs from a remote repository, but does not merge. Updates remote-tracking branches.
* git pull <remote-name> <remote-branch-name> Fetch changes from a remote branch and merge them into the current local branch (`fetch` + `merge`). If tracking is set up, often just `git pull`.
* git push <remote-name> <local-branch-name> Upload local commits from the local branch to the remote repository.
* git push -u <remote-name> <local-branch-name> Push the branch and set it up to track the remote branch. Subsequent pushes can be just `git push`.
* git push <remote-name> --delete <remote-branch-name> Delete a branch on the remote repository.
* git push <remote-name> --tags Push all local tags to the remote repository.

**Exploring History & Objects**

* git log Show the commit history.
* git log --oneline Show a condensed commit history (one line per commit).
* git log --graph --oneline --all Visualize the commit history graph for all branches.
* git log -p Show the commit history with the diff for each commit.
* git show <commit-ish> Show information about a commit, tag, or branch, including the changes introduced by the commit.
* git blame <file-path> Show author and commit information for each line in a file.
* git reflog Show a log of where HEAD and branch refs have been. Useful for recovering lost commits.

**Undoing Changes**

* git restore <file-path> Discard unstaged changes in the working directory for a specific file. **Caution: Destructive.**
* git checkout -- <file-path> Older command to discard unstaged changes in the working directory for a specific file. **Caution: Destructive.**
* git reset HEAD <file-path> Unstage changes for a specific file, keeping them in the working directory (`--mixed` mode).
* git restore --staged <file-path> Unstage changes for a specific file, keeping them in the working directory.
* git reset --soft <commit-hash> Move HEAD to the specified commit. Keep changes from discarded commits staged. **Caution: Rewrites history.**
* git reset --mixed <commit-hash> (or just git reset <commit-hash>) Move HEAD to the specified commit. Unstage changes from discarded commits, keep in working directory. **Caution: Rewrites history.**
* git reset --hard <commit-hash> Move HEAD to the specified commit, discard staged changes, and discard all changes in the working directory after the commit. **Caution: Destructive and rewrites history.**
* git revert <commit-hash> Create a *new* commit that undoes the changes of a specific commit. Safe for shared history.

**Stashing**

* git stash Save local modifications (staged and unstaged) temporarily and revert the working directory to a clean state.
* git stash list List all stashed changes.
* git stash apply Apply the most recent stash to the working directory. Stash remains in the list.
* git stash pop Apply the most recent stash and remove it from the list.
* git stash drop <stash@{n}> Remove a specific stash from the list.
* git stash clear Remove all stashed entries. **Caution: Destructive.**

This reference covers the primary commands discussed. Git has many more options and commands, but mastering these provides a strong foundation for professional use.