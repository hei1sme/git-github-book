## Chapter 4: Branching and Merging - Working on Different Ideas

One of Git's killer features is its lightweight and efficient branching model. Branches allow you to diverge from the main line of development, work on new features or fixes in isolation, and later integrate those changes back. This is fundamental for parallel development and experimentation, both for individuals and teams.

### 4.1 Understanding Branches

Imagine your project's commit history as a timeline. Each commit is a point on that line. A branch is simply a lightweight, movable pointer to one of these commits. The default branch created by `git init` or `git clone` is typically named `main` (or `master`).

When you make a new commit, the branch pointer you are currently on automatically moves forward to point to this new commit.

**[Insert Diagram: Simple Commit History with Main Branch Pointer]**

#### 4.1.1 Why Use Branches?

Branches provide a safe environment to:

* **Develop New Features:** Work on a feature without destabilizing the main codebase.
* **Fix Bugs:** Isolate bug fixes from ongoing feature development.
* **Experiment:** Try out radical ideas without committing them to the main project history unless they work out.
* **Facilitate Collaboration:** Multiple developers can work on different features or tasks simultaneously on their own branches.

Branches are cheap and easy to create and merge in Git, making them a core part of everyday workflow.

#### 4.1.2 The `main` (or `master`) Branch

By convention, the `main` (or `master`) branch is considered the stable or production-ready version of the project. Development typically happens on other branches, and changes are integrated back into `main` once they are complete and reviewed.

While the name "main" is the modern standard, you will still encounter "master" in older repositories or documentation. They serve the same purpose.

### 4.2 Branch Operations

Let's learn the basic commands for managing branches. We'll use our `my-first-git-project` (or any Git repository you have) for practice.

Make sure you are in your project directory.

#### 4.2.1 Listing Branches (`git branch`)

To see the branches in your local repository, use `git branch`:

```bash
git branch
```

Output might look like this:

```
* main
```

The asterisk (`*`) indicates the branch you are currently on (your `HEAD`).

If you had other branches, they would also be listed:

```
  feature/new-feature
* main
  bugfix/login-issue
```

To see both local and remote-tracking branches (like `origin/main`), use the `-a` flag:

```bash
git branch -a
```

```
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
```

#### 4.2.2 Creating a New Branch (`git branch <branch-name>`)

To create a new branch, simply use `git branch` followed by the desired name. Let's create a branch for a new feature.

```bash
git branch develop-feature-x
git branch
```

Output:

```
  develop-feature-x
* main
```

You've created a new branch named `develop-feature-x`. Notice that you are still on the `main` branch (`*` is next to `main`). Creating a branch only creates the new pointer; it doesn't automatically switch you to it. The new branch pointer initially points to the *same commit* that your current branch (`main`) points to.

#### 4.2.3 Switching Between Branches (`git checkout`, `git switch`)

To start working on the new branch, you need to switch to it. Git provides two commands for this: the older `git checkout` (which has many uses) and the newer, more focused `git switch`. It's recommended to use `git switch` for clarity when simply changing branches.

To switch to the `develop-feature-x` branch:

```bash
git switch develop-feature-x
```

Output:

```
Switched to branch 'develop-feature-x'
```

Now, run `git branch` again:

```bash
git branch
```

Output:

```
* develop-feature-x
  main
```

The asterisk shows you are now on the `develop-feature-x` branch. Your working directory is updated to match the snapshot of the commit this branch points to. Any new commits you make will be added to the `develop-feature-x` branch, moving its pointer forward.

You can switch back to main:

```bash
git switch main
git branch
```

Output:

```
  develop-feature-x
* main
```

#### 4.2.4 Creating and Switching Simultaneously (`git checkout -b`, `git switch -c`)

It's very common to create a new branch and immediately want to start working on it. You can combine the creation and switching steps into a single command.

Using `git checkout`:

```bash
git checkout -b another-feature
```

Using `git switch`:

```bash
git switch -c yet-another-feature
```

Both commands achieve the same result: create a new branch with the specified name (pointing to the current commit) and then switch your `HEAD` to that new branch.

#### 4.2.5 Renaming a Branch (`git branch -m`)

To rename a local branch:

```bash
# While on a DIFFERENT branch:
git branch -m <old-name> <new-name>

# While on the branch you want to rename:
git branch -m <new-name>
```

Example: Rename `develop-feature-x` to `feature/feature-x-dev` while you are on the `main` branch.

```bash
git switch main # Make sure you are not on the branch you want to rename
git branch -m develop-feature-x feature/feature-x-dev
git branch
```

Output:

```
  feature/feature-x-dev
* main
```

#### 4.2.6 Deleting a Branch (`git branch -d`)

Once you're done with a branch (e.g., after merging its changes elsewhere), you can delete it.

To delete a local branch, use the `-d` flag. This flag is a "safe" delete – Git will only delete the branch if it has been fully merged into its upstream branch or HEAD (to prevent losing work).

```bash
# Make sure you are not on the branch you want to delete
git switch main
git branch -d feature/feature-x-dev
```

Output:

```
Deleted branch feature/feature-x-dev (was abc123d). # abc123d is the last commit hash
```

If the branch contains commits that haven't been merged, Git will warn you and refuse to delete it with `-d`. To force deletion (use with caution! you might lose commits!), use the `-D` flag:

```bash
git branch -D branch-with-unmerged-commits
```

**Deleting Remote Branches:** Deleting a local branch *does not* delete its counterpart on the remote (like GitHub). To delete a remote branch, you use `git push` with the `--delete` flag:

```bash
git push origin --delete <branch-name-on-remote>
```

Example: `git push origin --delete feature/feature-x-dev`

### 4.3 Merging Branches

Merging is the process of integrating changes from one branch into another. When you merge, Git combines the commit histories of the two branches.

Let's create a simple scenario to demonstrate merging.

1. Start on the `main` branch.
2. Create and switch to a new branch, say `feature/add-greeting`.

   ```bash
   git switch -c feature/add-greeting
   ```
3. Make some changes and commit them on this new branch.

   ```bash
   echo "Hello, Git users!" > greeting.txt
   git add .
   git commit -m "Add greeting file"
   ```
4. Switch back to the `main` branch.

   ```bash
   git switch main
   ```

    Notice that `greeting.txt` is no longer in your working directory. This is because `main` hasn't received the changes from `feature/add-greeting` yet.

Now, merge the `feature/add-greeting` branch into `main`:

```bash
git merge feature/add-greeting
```

Git will attempt to combine the histories.

#### 4.3.1 The Merge Process (`git merge <branch-to-merge>`)

When you run `git merge <branch-to-merge>`, Git finds the common ancestor commit of your current branch (the one you are merging *into*) and the branch you are merging *from*. It then combines the changes introduced in each branch since that common ancestor.

There are two main types of merges:

#### 4.3.2 Fast-Forward Merge vs. Three-Way Merge

* **Fast-Forward Merge:** This happens when there is a linear path from the commit your current branch points to, to the tip of the branch you are merging. In this case, Git simply moves the pointer of your current branch forward to the latest commit of the merged branch. No new merge commit is created.

  **[Insert Diagram: Fast-Forward Merge - Showing main pointer moving forward to feature branch tip]**

  Our `feature/add-greeting` example will likely result in a fast-forward merge if you haven't made any commits on `main` since creating the feature branch.

  ```bash
  # After running git merge feature/add-greeting
  # (Assuming no commits on main since feature branch was created)
  Updating abc123d..def456g
  Fast-forward
   greeting.txt | 1 +
   1 file changed, 1 insertion(+)
   create mode 100644 greeting.txt
  ```

  Now, check your working directory (`greeting.txt` should be there) and `git log --oneline`:

  ```bash
  git log --oneline
  ```

  Output:

  ```
  def456g (HEAD -> main, feature/add-greeting) Add greeting file
  872a3b3 Initial project setup with README and first file
  ```

  The `main` pointer just moved forward.
* **Three-Way Merge:** This happens when the history of your current branch has diverged from the history of the branch you are merging (i.e., you've made commits on both branches since they diverged). Git uses the two branch tips and their common ancestor to create a new "merge commit" that ties the two histories together.

  **[Insert Diagram: Three-Way Merge - Showing two branches diverging and a new merge commit combining them]**

  Let's simulate this:
  1. On `main`, add a commit.

     ```bash
     echo "Project version 1.0" > version.txt
     git add .
     git commit -m "Add version file"
     ```
  2. Switch back to `feature/add-greeting`. Make another commit.

     ```bash
     git switch feature/add-greeting
     echo "Goodbye message too." >> greeting.txt
     git add .
     git commit -m "Add goodbye to greeting file"
     ```
  3. Switch back to `main` and merge `feature/add-greeting` again.

     ```bash
     git switch main
     git merge feature/add-greeting
     ```

  This time, Git will perform a three-way merge and likely open your text editor to write a merge commit message (Git provides a default one).

  ```bash
  Merge branch 'feature/add-greeting'
  ```

  Save and close the editor. The output will show a merge commit being created:

  ```bash
  Merge made by the 'recursive' strategy.
   greeting.txt | 1 +
   1 file changed, 1 insertion(+)
  ```

  Now, `git log --oneline` will show three commits: the initial, the version commit on main, and the new merge commit connecting `main` and `feature/add-greeting`.

  ```bash
  hij789k (HEAD -> main) Merge branch 'feature/add-greeting'
  klm012n (feature/add-greeting) Add goodbye to greeting file
  abc123d Add version file # This is on main's history
  872a3b3 Initial project setup with README and first file
  ```

  Note how the merge commit `hij789k` has two parent commits (klm012n and abc123d), representing the points where the branches were merged.

#### 4.3.3 Resolving Merge Conflicts

Merges are not always automatic. A **merge conflict** occurs when Git attempts to merge changes, but both branches have modified the *same* lines in the *same* file, or when one branch deleted a file that the other branch modified. Git doesn't know which change to keep, so it pauses the merge process and asks you to manually resolve the conflict.

Let's create a conflict scenario:

1. Make sure you are on the `main` branch.
2. Modify `first_file.txt`.

   ```bash
   git switch main
   echo "Modified on main." > first_file.txt # Overwrite content for simple conflict
   git add .
   git commit -m "Change first file on main"
   ```
3. Switch to the `feature/add-greeting` branch (if you deleted it, recreate it and make a different change). Modify `first_file.txt` *differently*.

   ```bash
   git switch feature/add-greeting # Or git switch -c feature/add-greeting if deleted
   echo "Modified on feature." > first_file.txt # Overwrite differently
   git add .
   git commit -m "Change first file on feature branch"
   ```
4. Switch back to `main` and attempt to merge `feature/add-greeting`.

   ```bash
   git switch main
   git merge feature/add-greeting
   ```

Git will stop and report a merge conflict:

```bash
Auto-merging first_file.txt
CONFLICT (content): Merge conflict in first_file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

`git status` is your friend here:

```bash
git status
```

Output will indicate you are in a merge state and list the conflicted file(s):

```
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   first_file.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

**Resolving the Conflict:**

1. **Identify Conflicts:** Open the conflicted file (`first_file.txt`). Git inserts special markers to show the conflicting sections:

   ```
   <<<<<<< HEAD
   Modified on main.
   =======
   Modified on feature.
   >>>>>>> feature/add-greeting
   ```
   * `<<<<<<< HEAD`: Marks the beginning of the conflicting change from the current branch (`HEAD`, which is `main` in this case).
   * `=======`: Separates the changes from the two branches.
   * `>>>>>>> feature/add-greeting`: Marks the end of the conflicting change from the branch being merged (`feature/add-greeting`).
2. **Edit the File:** Manually edit the file to resolve the conflict. Remove the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) and keep or combine the content as needed to achieve the desired final state.

   For example, you might decide to keep both lines or write a new line that combines the ideas:

   ```
   Modified on main.
   Modified on feature.
   # Or perhaps:
   This file was modified on both branches.
   ```
3. **Stage the Resolved File:** After editing, stage the file to tell Git that the conflict has been resolved for that file.

   ```bash
   git add first_file.txt
   ```

   `git status` will now show the file under "Changes to be committed".
4. **Commit the Merge:** Once all conflicts are resolved and staged, commit the merge. Git will automatically create a merge commit message.

   ```bash
   git commit
   ```

   Git will open your editor with a pre-filled message like "Merge branch 'feature/add-greeting' into main" with comments listing the conflicts that were resolved. Save and close the editor.

The merge is now complete, with a new merge commit incorporating the resolved changes.

**Aborting a Merge:** If you get into a messy merge conflict and want to start over, you can abort the merge process before committing:

```bash
git merge --abort
```

This will revert your repository back to the state it was in before you started the merge attempt.

### 4.4 Introduction to Rebasing

Merging is one way to integrate changes. **Rebasing** is another, offering a different approach to combining histories.

#### 4.4.1 What is Rebasing? (`git rebase`)

Rebasing is the process of moving or combining a sequence of commits to a new base commit. Instead of creating a merge commit, rebasing *rewrites* the commit history to make it look like the commits from your feature branch were made *directly on top of* the target branch's latest commit.

Imagine you have a `feature` branch that diverged from `main`.

**[Insert Diagram: Commit History Before Rebase - Showing main and feature diverging]**

When you rebase `feature` onto `main`, Git finds the common ancestor, gets the changes from your feature commits, and then reapplies those changes one by one onto the tip of the `main` branch.

```bash
# While on your feature branch
git switch feature
git rebase main
```

**[Insert Diagram: Commit History After Rebase - Showing feature commits moved onto the tip of main, linear history]**

The commits on the `feature` branch now have new commit hashes because they are rewritten and applied to a new base.

#### 4.4.2 Rebase vs. Merge: Choosing the Right Strategy

* **Merge:** Preserves the original history, including the fact that a feature branch diverged and was later merged. Creates a merge commit. History is a directed acyclic graph (DAG).
  * **Pros:** Accurate historical record, non-destructive (doesn't rewrite history).
  * **Cons:** Can create a messy commit history with many merge commits, especially in active repositories with short-lived branches.
* **Rebase:** Creates a linear project history, making it look like development happened sequentially. Rewrites commit history.
  * **Pros:** Clean, linear history that is easy to follow, avoids extra merge commits.
  * **Cons:** **Rewrites history**, which can be problematic on branches that have already been pushed to a shared remote repository (more on this later). Can introduce conflicts one commit at a time during the rebase process.

**General Guideline:**

* **Merge** when you want to preserve the exact historical record of when development diverged and merged. Recommended for integrating changes into long-lived public branches (like `main`).
* **Rebase** when you want to maintain a clean, linear history. Useful for incorporating upstream changes into your *local, private* feature branches *before* merging them into a shared branch. **Never rebase a branch that others are currently working on or that has been pushed to a public remote.**

#### 4.4.3 Interactive Rebasing (Introduction)

Rebasing has an interactive mode (`git rebase -i <base-commit-ish>`) which is a powerful tool for cleaning up your commit history *before* sharing it. It allows you to:

* Edit commit messages.
* Squash (combine) multiple commits into a single one.
* Reorder commits.
* Drop commits.
* Split commits.

Interactive rebasing is an advanced technique for polishing your local work history and will be touched upon briefly in Chapter 6.

**Chapter 4 Summary:**

You've learned the critical role of branches in Git for parallel development and experimentation. We covered how to list, create, switch between, rename, and delete branches (`git branch`, `git checkout`, `git switch`). We then explored the merge process, understanding the difference between fast-forward and three-way merges and how to resolve merge conflicts. Finally, we introduced rebasing as an alternative integration strategy, highlighting the key differences and use cases compared to merging.

Mastering branching and merging is essential for effective version control and is the foundation for collaborative workflows on platforms like GitHub, which we will delve into in the next chapter.