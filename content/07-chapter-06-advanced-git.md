## Chapter 6: Advanced Git Concepts and Commands

You've mastered the core Git workflow: cloning, adding, committing, branching, merging, pulling, and pushing. These are sufficient for most day-to-day tasks. However, Git has a rich set of commands for handling more complex situations, exploring history in depth, and managing your work in flexible ways. This chapter introduces some of these powerful advanced features.

### 6.1 Undoing Changes

Mistakes happen! Git provides several ways to undo changes, each suited for different scenarios. It's crucial to understand what each command does and how it affects your history.

#### 6.1.1 Amending Commits (`git commit --amend`)

If you've just made a commit but immediately realize you made a typo in the commit message or forgot to include a small change, you can *amend* the last commit.

* **How it works:** `git commit --amend` replaces the *last* commit with a *new* commit that includes the staged changes (if any) and uses the updated message. It effectively rewrites the history of the branch by discarding the old last commit and creating a replacement.
* **Use Case:** Fixing the very last commit *before* pushing it to a shared remote.

Let's try it:

1. Make a commit:

   ```bash
   echo "Initial content." > test.txt
   git add .
   git commit -m "Add test file"
   ```
2. Realize you want to change the commit message and add another line to the file.

   ```bash
   echo "More content." >> test.txt
   git add . # Stage the new change
   git commit --amend -m "Add test file with initial and more content"
   ```

Now, run `git log --oneline`. You'll see only one commit for this change, with the new message and the new file content. The original "Add test file" commit is gone.

**Caution:** **Never amend a commit that you have already pushed to a shared remote repository.** Amending rewrites history, and this will cause problems for anyone who has already pulled the original commit.

#### 6.1.2 Reverting Commits (`git revert`)

If you want to undo a commit that has *already been pushed* or that is further back in the history, and you want to do so safely without rewriting history, you use `git revert`.

* **How it works:** `git revert <commit-hash>` creates a *new* commit that undoes the changes introduced by the specified commit. The original commit remains in the history, and the revert commit adds a new point in history where those changes are cancelled out.
* **Use Case:** Safely undoing changes that are already part of shared history (e.g., reverting a faulty feature commit on `main`).

Example: Let's say the commit "Add test file with initial and more content" introduced a bug.

1. Find the commit hash using `git log --oneline`.

   ```bash
   git log --oneline
   ```

    Find the hash (e.g., `a1b2c3d`).
2. Revert that commit:

   ```bash
   git revert a1b2c3d
   ```

    Git will create a new commit that undoes `a1b2c3d`. Your editor will open with a default commit message like "Revert 'Add test file with initial and more content'". Save and close.

Now `git log --oneline` shows your original commit and a new revert commit:

```
e4f5g6h (HEAD -> main) Revert "Add test file with initial and more content"
a1b2c3d Add test file with initial and more content
... (previous commits)
```

The changes from `a1b2c3d` are now undone in the latest commit, but the history remains intact.

#### 6.1.3 Resetting Commits (`git reset` --soft, --mixed, --hard)

`git reset` is a powerful and potentially destructive command used to undo local changes or commits by moving the branch pointer (`HEAD`) to a different commit. Unlike `git revert`, `git reset` *rewrites history* by abandoning commits.

* **How it works:** `git reset <commit-ish>` moves the current branch pointer and potentially changes the staging area and working directory depending on the flag used. `<commit-ish>` can be a commit hash, a tag name, a branch name, or relative references like `HEAD~1` (the commit before HEAD), `HEAD~2` (two commits before HEAD), etc.
* **Use Case:** Undoing recent *local* commits that haven't been shared yet, unstaging changes, or discarding local changes.

Let's add two commits to demonstrate reset:

```bash
echo "First new commit." > file1.txt
git add .
git commit -m "Add file1"

echo "Second new commit." > file2.txt
git add .
git commit -m "Add file2"
```

`git log --oneline` now shows:

```
xyz987w (HEAD -> main) Add file2
vut654s Add file1
e4f5g6h Revert "Add test file with initial and more content"
...
```

Suppose you want to undo the last two commits (`Add file2` and `Add file1`). You want to move back to the state after the `Revert` commit (`e4f5g6h`).

**Different Reset Modes:**

* `git reset --soft <commit-hash>`:
  * Moves the branch pointer (`HEAD`) to the specified commit.
  * **Does NOT** change the staging area. The changes from the reset commits are now staged (as if you just ran `git add` on them).
  * **Does NOT** change the working directory. Your files remain as they were after the reset commits.
  * **Use Case:** Go back commits but keep all changes staged, maybe to recommit them as a single, better commit.

  ```bash
  git reset --soft e4f5g6h
  git status # Shows file1.txt and file2.txt as staged
  ```
* `git reset --mixed <commit-hash>`: (This is the default mode if no flag is given)
  * Moves the branch pointer (`HEAD`) to the specified commit.
  * Resets the staging area to match the target commit. The changes from the reset commits are **un-staged**.
  * **Does NOT** change the working directory. Your files remain as they were after the reset commits.
  * **Use Case:** Go back commits but keep all changes in your working directory, ready to be re-added and recommitted differently.

  ```bash
  git reset --mixed e4f5g6h # Same as git reset e4f5g6h
  git status # Shows file1.txt and file2.txt as unstaged
  ```
* `git reset --hard <commit-hash>`:
  * Moves the branch pointer (`HEAD`) to the specified commit.
  * Resets the staging area to match the target commit.
  * **Resets the working directory** to match the target commit. All changes made *after* the target commit are **discarded permanently**.
  * **Use Case:** Completely discard commits and all associated changes in the working directory. **This is dangerous and should be used with extreme caution!**

  ```bash
  git reset --hard e4f5g6h
  git status # Working tree clean
  # file1.txt and file2.txt are now gone from your working directory!
  ```

**Caution with** `git reset`**:** Because `git reset` rewrites history, you should **never** use it on commits that have already been pushed to a shared remote repository. This will cause divergence and force issues for collaborators. Use `git revert` instead for pushed commits.

#### 6.1.4 Recovering Lost Commits (`git reflog`)

What if you accidentally use `git reset --hard` or make another mistake that seems to lose commits? Don't panic (yet!). Git has a safety net called the **Reflog (Reference Log)**.

* **What it is:** The reflog is a log of where your `HEAD` and branch references have been in your local repository. Every time you commit, switch branches, merge, rebase, or reset, a new entry is added to the reflog. This log is *local* to your repository and not part of the shared history you push.
* **Use Case:** Recovering from mistakes that involved rewriting history or detaching HEAD.

To see your reflog:

```bash
git reflog
```

You'll see output showing your recent Git actions:

```
e4f5g6h (HEAD -> main) HEAD@{0}: reset: moving to e4f5g6h
xyz987w HEAD@{1}: commit: Add file2
vut654s HEAD@{2}: commit: Add file1
e4f5g6h HEAD@{3}: commit: Revert "Add test file with initial and more content"
a1b2c3d HEAD@{4}: commit: Add test file with initial and more content
...
```

Each line shows a position your `HEAD` (or another reference) was pointing to, along with the command that caused the move. `HEAD@{0}` is your current position, `HEAD@{1}` is where you were one step ago, and so on.

If you accidentally `git reset --hard` back to `e4f5g6h` (losing `vut654s` and `xyz987w`), you can see in the reflog that `HEAD@{1}` was at `xyz987w`. To recover, you can reset back to that state:

```bash
git reset --hard HEAD@{1}
# OR using the commit hash from reflog:
# git reset --hard xyz987w
```

This moves your `HEAD` back to the commit `xyz987w`, and because we used `--hard`, your working directory and staging area are restored to that state as well. The reflog is your safety net when you think you've lost commits locally.

### 6.2 Working with Tags

Tags are used to mark specific points in history as important. They are typically used to mark release points (e.g., v1.0, v2.0-beta).

#### 6.2.1 Creating Tags (`git tag`)

There are two types of tags:

* **Lightweight Tags:** A simple pointer to a commit, like a branch that doesn't move. They are just a name and a commit.

  ```bash
  git tag v1.0-lw HEAD # Tag the current commit
  git tag v1.0-lw abc123d # Tag a specific commit by hash
  ```
* **Annotated Tags:** Stored as full objects in the Git database. They contain a tagger name, email, date, and a tagging message. They are signed and verifiable. **Annotated tags are recommended** because they contain metadata about the release.

  ```bash
  git tag -a v1.0 -m "Release version 1.0" HEAD # Tag current commit with message
  git tag -a v1.0 abc123d -m "Release version 1.0" # Tag specific commit
  ```

  Git will open your editor for the message if `-m` is not used.

To list tags:

```bash
git tag
```

To list tags with their messages:

```bash
git tag -n
```

To see information about a specific tag:

```bash
git show v1.0
```

#### 6.2.2 Pushing Tags

By default, `git push` does *not* push tags to the remote. You must explicitly push tags.

```bash
git push origin <tag-name> # Push a specific tag
git push origin --tags # Push all local tags to the remote
```

#### 6.2.3 Checking Out Tags

You can "check out" a tag to see the state of the repository at that specific tagged point in history:

```bash
git checkout v1.0
```

**Important:** When you check out a tag (or a specific commit hash), you enter a **"detached HEAD"** state. This means your `HEAD` is pointing directly to a commit, not to a branch pointer. If you make new commits in this state, they will not belong to any branch and can be lost unless you create a new branch from that commit before switching away.

```bash
# Example: Make changes and commit in detached HEAD state (DANGEROUS!)
# Make changes...
# git commit -m "Work done in detached HEAD"
# git switch main # You lose reference to the new commit unless you noted its hash

# Correct way: Create a new branch from the tag/commit
git checkout -b hotfix/v1.0 v1.0
# Now you are on the new branch hotfix/v1.0, pointing to the v1.0 commit.
# Make changes and commits on this new branch.
```

### 6.3 Stashing Changes (`git stash`)

Sometimes you're working on a feature, but you need to quickly switch to another branch to fix a bug or pull in urgent changes. You don't want to commit your half-finished work. `git stash` saves your current changes (both staged and unstaged) and reverts your working directory to the state of the `HEAD` commit, allowing you to switch branches cleanly.

* **How it works:** `git stash` takes your changes and saves them on a temporary "stash" stack. Your working directory becomes clean.
* **Use Case:** Temporarily save incomplete work to switch context.

1. Make some changes to files that are tracked.

   ```bash
   echo "WIP changes." >> first_file.txt
   # Optionally stage some of them
   # git add first_file.txt
   ```

   `git status` will show changes.
2. Stash the changes:

   ```bash
   git stash
   ```

    Output: `Saved working tree and index state WIP on main: ...` `git status` will show a clean working directory. Your uncommitted changes are now safely stored.

#### 6.3.1 Saving Uncommitted Changes

`git stash` by default stashes changes that are tracked (modified or staged). It *doesn't* stash untracked files or ignored files. To stash untracked files as well, use `git stash -u` or `git stash --include-untracked`. To stash ignored files too, use `git stash -a` or `git stash --all`.

You can stash with a message for easier identification later:

```bash
git stash save "Work on dashboard layout part 1"
# Or the newer syntax:
# git stash push -m "Work on dashboard layout part 1"
```

To view your stash list:

```bash
git stash list
```

Output:

```
stash@{0}: WIP on main: ...
stash@{1}: On feature/branch: Work on dashboard layout part 1
...
```

The most recent stash is `stash@{0}`.

#### 6.3.2 Applying and Dropping Stashes

To bring the stashed changes back to your working directory:

* `git stash apply`: Applies the most recent stash (`stash@{0}`) to your working directory. The stash remains in the stash list.

  ```bash
  git stash apply
  ```

   To apply a specific stash: `git stash apply stash@{1}`
* `git stash pop`: Applies the most recent stash (`stash@{0}`) and *removes* it from the stash list. Use this when you're done with the stashed changes.

  ```bash
  git stash pop
  ```

   To pop a specific stash: `git stash pop stash@{1}`

If applying a stash results in conflicts with your current working directory, Git will report them, and you'll need to resolve them just like merge conflicts.

To remove a stash without applying it:

```bash
git stash drop stash@{1} # Drop a specific stash
git stash clear # Drop all stashes (use with caution!)
```

#### 6.3.3 Stashing Specific Files

You can stash only specific files or parts of files, though this is less common than stashing everything. This often involves using `git stash push -- <file1> <file2> ...` or using `git stash --patch` for interactive stashing of hunks within files. These are more advanced uses.

### 6.4 Exploring Git History

`git log` is the basic command to view history, but Git provides powerful tools to explore the commit graph, see differences between commits, and understand who changed what.

#### 6.4.1 Advanced `git log` Options

* `git log --oneline`: Compact view, one commit per line. Very useful.
* `git log --graph`: Draws a text-based graph representation of the branches and merges. Great for visualizing history. Combine with `--oneline`: `git log --oneline --graph`.
* `git log --all`: Shows commits from all branches, not just the current one. Combine with `--graph --oneline`: `git log --all --graph --oneline`.
* `git log -p`: Shows the patch (diff) introduced by each commit.
* `git log -<n>`: Show only the last `<n>` commits (e.g., `git log -5`).
* `git log --author="Your Name"`: Filter commits by author.
* `git log --grep="<pattern>"`: Filter commits by keyword in the message.
* `git log --since="<date>"`, `--until="<date>"`: Filter commits by date.
* `git log <branch1>..<branch2>`: Show commits present in `branch2` but not in `branch1`.
* `git log <commit1>..<commit2>`: Show commits between `commit1` and `commit2`.
* `git log --merges`: Show only merge commits.
* `git log -- <file-path>`: Show commits that affected a specific file.

Example: View the last 10 commits affecting `first_file.txt`, showing the diff for each:

```bash
git log -10 -p -- first_file.txt
```

#### 6.4.2 Comparing Changes (`git diff`)

`git diff` is used to show the differences between various points in your repository:

* `git diff`: Show changes in the working directory that are *not staged*.
* `git diff --staged` (or `--cached`): Show changes in the staging area that are *not* yet in the last commit.
* `git diff HEAD`: Show changes in the working directory and staging area compared to the last commit.
* `git diff <commit1> <commit2>`: Show the difference between two specific commits.
* `git diff <branch1> <branch2>`: Show the difference between the tips of two branches.
* `git diff <commit> -- <file-path>`: Show changes in a specific file since a commit.

Example: See the changes you've made since your last commit, including both staged and unstaged modifications.

```bash
git diff HEAD
```

Example: Compare the `main` branch to the `feature/new-dashboard` branch.

```bash
git diff main feature/new-dashboard
```

#### 6.4.3 Blaming Lines (`git blame`)

`git blame <file-path>` shows who last modified each line of a file and in which commit.

* **Use Case:** Identifying when and why a specific line of code was changed or introduced a bug.

```bash
git blame first_file.txt
```

Output shows each line, the commit hash where it was last changed, the author, and the timestamp:

```
a1b2c3d (Your Name 2024-01-08 10:00:00 -0500 1) This file was modified on both branches.
```

You can combine `git blame` with line ranges (`git blame -L <start>,<end> <file>`) or view the blame at a specific commit (`git blame <commit> -- <file>`).

### 6.5 Git Hooks (Introduction)

Git hooks are scripts that Git executes automatically at certain points in its workflow (e.g., before committing, after receiving a push). They allow you to automate tasks, enforce policies, or integrate with external systems.

* **How they work:** Hooks are executable scripts located in the `.git/hooks` directory of your repository. Git looks for scripts with specific names (e.g., `pre-commit`, `post-commit`, `pre-push`). If a script with a hook name exists and is executable, Git runs it at the corresponding point.

#### 6.5.1 Client-Side Hooks

Run on the developer's local machine. They can be skipped by users.

* `pre-commit`**:** Runs before a commit is finalized. Useful for running linters, formatters, or basic tests to ensure committed code meets certain standards. If the script exits with a non-zero status, the commit is aborted.
* `prepare-commit-msg`**:** Runs before the commit message editor is launched. Can be used to generate a default commit message.
* `commit-msg`**:** Runs after the commit message editor closes but before the commit is created. Used to validate commit messages (e.g., check format, required keywords). Aborts the commit if the script fails.
* `post-commit`**:** Runs after a commit is completed. Useful for triggering notifications or other non-altering actions.
* `pre-push`**:** Runs before `git push`. Can be used to run tests or checks on the commits being pushed to prevent pushing bad code. Aborts the push if it fails.

To use a hook, rename the sample hook file (e.g., `.git/hooks/pre-commit.sample`) to the hook name (`.git/hooks/pre-commit`) and make it executable.

#### 6.5.2 Server-Side Hooks (Briefly)

Run on the remote repository server (like GitHub, though GitHub has its own implementation with Webhooks and GitHub Actions which are generally more powerful). They are often used to enforce policies that client-side hooks can't guarantee (since clients can be skipped). Examples include `pre-receive` (runs before updates are applied to the remote repository) and `post-receive` (runs after a push is accepted).

While client-side hooks are local and optional, server-side hooks or platform features like GitHub Actions are necessary to enforce rules for all contributors to a shared repository.

**Chapter 6 Summary:**

We explored advanced ways to manage and explore your Git history. You learned how to safely undo recent local commits (`git commit --amend`), how to safely undo committed changes while preserving history (`git revert`), and how to use `git reset` in its different modes (`--soft`, `--mixed`, `--hard`) to move branch pointers and reset the staging area or working directory (with caution!). We saw how `git reflog` can be a lifesaver for recovering seemingly lost commits. We also covered using tags for marking release points (`git tag`), pushing tags, and understanding detached HEAD when checking out tags. Finally, we looked at how to explore history more deeply with advanced `git log` and `git diff` options and how `git blame` helps identify who changed specific lines. We concluded with an introduction to Git hooks for automating tasks based on Git events.

You now have a more robust toolkit for managing your Git repositories and history effectively.