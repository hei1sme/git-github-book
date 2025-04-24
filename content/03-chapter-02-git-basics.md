## Chapter 2: Git Basics - Getting Started with Your First Repository

Now that you understand *why* version control is important and the roles of Git and GitHub, this chapter will walk you through the essential first steps: installing Git, setting up your identity, creating a new project repository, or cloning an existing one, and making your first commits.

### 2.1 Git Installation and Initial Configuration

You should have installed Git in the previous chapter. Let's verify and then perform a crucial initial setup step.

#### 2.1.1 Verifying Git Installation

Open your terminal or Git Bash. Type the following command:

```bash
git --version
```

You should see output similar to `git version 2.38.1` (the exact version number might differ). If you see this, Git is correctly installed and accessible from your command line. If not, revisit the installation steps from Chapter 1.

#### 2.1.2 Setting Your User Name and Email (`git config`)

Every time you make a commit in Git, it records the author's name and email address. This information is embedded in the commit history and helps track who made which changes. It's essential to set this up correctly.

You'll use the `git config` command to set global configuration options.

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

**Important:** Replace `"Your Name"` with your actual name (or the name you want associated with your commits) and `"your.email@example.com"` with your email address. It's generally recommended to use the same email address associated with your GitHub account, especially if you plan to contribute to GitHub repositories.

The `--global` flag means this configuration will apply to *all* Git repositories on your system. If you ever need a different name/email for a specific project, you can run these commands *without* `--global` inside that project's directory.

You can verify your settings by typing:

```bash
git config --global user.name
git config --global user.email
```

Or view all global configurations:

```bash
git config --global --list
```

### 2.2 Creating a New Local Repository (`git init`)

Let's start a new project from scratch and turn it into a Git repository.

First, create a directory for your project and navigate into it using your terminal:

```bash
mkdir my-first-git-project
cd my-first-git-project
```

Now, initialize Git in this directory:

```bash
git init
```

This command creates a new, empty Git repository or reinitializes an existing one in the current directory.

You will see output similar to:

```
Initialized empty Git repository in /path/to/my-first-git-project/.git/
```

#### 2.2.1 Initializing a Project Directory

By running `git init`, you've just told Git to start tracking changes in the `my-first-git-project` directory. At this moment, the directory is still empty (except for the hidden `.git` folder).

Let's create a simple file.

```bash
# Create a file (using touch on macOS/Linux or echo on Windows)
echo "# My First Git Project" > README.md
```

Now, let's check the status of our repository:

```bash
git status
```

You will see output indicating that you are on the `main` branch (or `master` on older Git versions) and that there is an untracked file (`README.md`):

```
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	README.md

nothing added to commit but untracked files present (use "git add" to track)
```

This output tells us:

* We are on the `main` branch. The default branch name was recently changed in Git and GitHub from `master` to `main`.
* There are no commits yet.
* We have a new file, `README.md`, that Git sees but is not currently tracking.

#### 2.2.2 The `.git` Folder

The `git init` command created a hidden directory named `.git` inside your project folder. This directory contains *everything* that Git needs to manage your project's history – objects (like commits, trees, and blobs), references (like branches and tags), configuration files, hooks, and more.

**You should generally never manually modify or delete files within the** `.git` **directory**, as it can corrupt your repository. Git commands are designed to interact with this folder safely.

### 2.3 Cloning an Existing Repository (`git clone`)

Often, you'll want to start working on a project that already exists, perhaps hosted on GitHub or another remote location. In this case, you **clone** the repository. Cloning downloads a full copy of the repository, including its entire history, and automatically sets up a connection to the original remote repository.

Let's clone a sample repository (e.g., a simple GitHub repo). We'll use a placeholder URL for now, and you'll use real GitHub URLs later.

Navigate to a directory where you want to place the cloned project (e.g., outside of `my-first-git-project`):

```bash
cd ..
```

Now, clone the repository using its URL:

```bash
git clone https://github.com/github/training-kit.git
```

Replace the URL with the actual URL of the repository you want to clone. You can find this URL on the repository's page on GitHub (usually under a "Code" button).

#### 2.3.1 Cloning from a Remote URL

When you run `git clone <repository-url>`, Git does several things:

1. It creates a new directory named after the repository (e.g., `training-kit` in the example above).
2. It initializes a new Git repository *inside* that directory.
3. It fetches all the data (commits, branches, etc.) from the remote repository.
4. It checks out the default branch (usually `main` or `master`).
5. It automatically sets up a "remote" named `origin` pointing to the original repository URL.

Navigate into the cloned directory:

```bash
cd training-kit
```

You can now explore the files and the Git history of this project. Running `git status` will show you are on the main branch and your working directory is clean (matches the latest commit).

#### 2.3.2 Cloning Options

The `git clone` command has several useful options:

* `git clone <url> <directory-name>`: Clone the repository into a specific directory name instead of the default.
* `git clone --depth <number> <url>`: Clone only the last `<number>` commits. Useful for large repositories where you don't need the full history.
* `git clone --branch <branch-name> <url>`: Clone a specific branch instead of the default.

For most cases, a simple `git clone <url>` is sufficient.

### 2.4 The Git Workflow: Stage, Commit, Push

The fundamental cycle of working with Git involves making changes, selecting which changes to save, saving them as a snapshot, and then sharing those snapshots. This is often described as the **Stage, Commit, Push** workflow.

Let's go back to our `my-first-git-project`:

```bash
cd ../my-first-git-project
```

We created `README.md` earlier, and `git status` showed it as "untracked".

#### 2.4.1 Checking Status (`git status`)

As you saw, `git status` is your constant companion. It tells you the current state of your working directory and staging area, showing:

* Which branch you're on.
* Files that have been changed but not staged.
* Files that are staged and ready to be committed.
* Untracked files (new files Git doesn't know about yet).
* Whether your branch is ahead of, behind, or in sync with its remote counterpart.

Let's create another file:

```bash
echo "This is the content of my first file." > first_file.txt
git status
```

Now `git status` will show both `README.md` and `first_file.txt` as untracked.

#### 2.4.2 Staging Changes (`git add`)

Before you can commit changes, you need to add them to the staging area. The staging area is where you build up the snapshot that will become your next commit. You use the `git add` command for this.

To stage `README.md`:

```bash
git add README.md
git status
```

`git status` will now show `README.md` under "Changes to be committed":

```
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	first_file.txt
```

`first_file.txt` is still untracked. You can add multiple files or even directories at once:

```bash
git add first_file.txt
git status
```

Now both files are staged:

```
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   README.md
	new file:   first_file.txt
```

You can also stage all changes in the current directory (including new, modified, and deleted files, but *not* untracked files unless you use `git add .`) with:

```bash
git add .
```

Or stage all *tracked* changes (modified and deleted, but *not* new untracked files) with:

```bash
git add -u
```

Or stage *all* changes (new, modified, deleted) including untracked files in the current directory and subdirectories:

```bash
git add -A
```

Using `git add .` is very common when you want to stage all changes in your current working directory.

#### 2.4.3 Committing Changes (`git commit`)

Once you've staged the changes you want to save as a snapshot, you **commit** them. This creates a permanent snapshot in the repository's history. Each commit requires a commit message describing *what* changes were made and *why*. Good commit messages are crucial for understanding the project's history later.

To commit the staged changes:

```bash
git commit -m "Initial project setup with README and first file"
```

The `-m` flag allows you to provide a short commit message directly on the command line. For longer messages, run `git commit` without `-m`, and Git will open your default text editor.

Output will look something like this:

```
[main (root-commit) 872a3b3] Initial project setup with README and first file
 2 files changed, 2 insertions(+)
 create mode 100644 README.md
 create mode 100644 first_file.txt
```

This shows the branch (`main`), a unique identifier for the commit (`872a3b3` - the first 7 characters of the SHA-1 hash), the commit message, and a summary of the changes (2 files changed, 2 lines inserted).

Now, run `git status` again:

```bash
git status
```

```
On branch main
nothing to commit, working tree clean
```

This indicates that your working directory matches the latest commit; there are no staged or unstaged changes.

#### 2.4.4 Viewing History (`git log`)

To see the history of commits in your repository, use the `git log` command:

```bash
git log
```

You'll see output like this (details will vary):

```
commit 872a3b3f1b1d9c0e0f8a7d6e5c4b2a1f0e9b8c7d (HEAD -> main)
Author: Your Name <your.email@example.com>
Date:   Mon Jan 8 10:00:00 2024 -0500

    Initial project setup with README and first file
```

This shows the full commit hash, author, date, and the commit message. As you make more commits, `git log` will list them in reverse chronological order (newest first).

`git log` has many useful options for viewing history, which we'll explore later (e.g., `--oneline`, `--graph`, `--all`).

#### 2.4.5 Pushing Changes (`git push`)

The `git commit` command saves changes to your *local* repository. If you are working with a remote repository (like one on GitHub), you need to **push** your committed changes to the remote to share them with others or back them up online.

Since our `my-first-git-project` was initialized locally, it doesn't currently have a remote counterpart. We'll cover connecting a local repository to a remote GitHub repository in Chapter 3.

For a repository cloned from a remote (like the `training-kit` example), you would use `git push` to send your local commits to the `origin` remote:

```bash
# Inside a cloned repository, after making commits
git push origin main
```

This command means: "Push my committed changes from my current branch (`main`) to the `origin` remote."

**Professional Tip:** It's common practice to `git pull` (to get the latest changes from the remote) *before* you start making changes and *before* you `git push` to minimize potential merge conflicts.

### 2.5 Modifying and Deleting Files

Let's practice modifying and deleting files and see how Git tracks these changes.

Go back to `my-first-git-project`. Open `first_file.txt` in a text editor and add a new line.

```bash
# Example using echo - you'd typically use a text editor
echo "Adding a second line." >> first_file.txt
git status
```

`git status` will now show `first_file.txt` under "Changes not staged for commit":

```
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   first_file.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

This means Git sees you've modified `first_file.txt` since the last commit, but it hasn't been added to the staging area yet.

Now, let's delete `README.md`.

```bash
rm README.md  # Use 'del README.md' on Windows command prompt
git status
```

`git status` will show `README.md` under "Changes not staged for commit", indicating it has been deleted:

```
On branch main
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   first_file.txt
	deleted:    README.md

no changes added to commit (use "git add" and/or "git commit -a")
```

To save these changes (modification of `first_file.txt` and deletion of `README.md`) in a new commit, you first need to stage them.

```bash
git add first_file.txt
git add README.md # Yes, you 'add' deleted files to stage the deletion
git status
```

`git status` now shows the changes are staged:

```
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   first_file.txt
	deleted:    README.md
```

Now, commit the staged changes:

```bash
git commit -m "Update first_file.txt and remove README.md"
```

And check the log:

```bash
git log --oneline
```

The `--oneline` flag shows a condensed view of the log. You'll see your two commits:

```
a1b2c3d (HEAD -> main) Update first_file.txt and remove README.md
872a3b3 Initial project setup with README and first file
```

#### 2.5.2 Unstaging Changes (`git reset`)

What if you stage a change (`git add`) but then decide you don't want it in the next commit? You can **unstage** it.

Make another modification to `first_file.txt`. Stage it.

```bash
echo "Another line." >> first_file.txt
git add first_file.txt
git status
```

Now `first_file.txt` is staged. If you change your mind, use `git reset` (or the suggested command from `git status`):

```bash
git reset HEAD first_file.txt # Older syntax, still common
# OR use the suggested command from git status
git restore --staged first_file.txt
git status
```

The `git status` output will show `first_file.txt` back in the "Changes not staged for commit" section. The staged change was removed, but the actual modification in the file is still there in your working directory.

#### 2.5.3 Discarding Local Changes (`git checkout`, `git restore`)

Sometimes you make changes in your working directory, realize they are wrong or unnecessary, and want to completely discard them, reverting the file back to its state in the last commit.

Make a change to `first_file.txt` again, but *don't* stage it this time.

```bash
echo "This line is a mistake." >> first_file.txt
git status
```

`git status` shows it as "Changes not staged for commit". To discard this modification, use `git checkout` or the newer, clearer `git restore`:

```bash
git checkout -- first_file.txt # Older syntax
# OR use the suggested command from git status
git restore first_file.txt
```

**Caution:** This command is powerful! It discards your local changes to that file *permanently*. There's no "undo" for this within Git, unless you had committed the changes previously.

After running the command, open `first_file.txt` again. The line "This line is a mistake." will be gone. `git status` will show a clean working directory (unless you have other unstaged changes).

**To discard ALL unstaged changes** in your working directory:

```bash
git checkout -- .
# OR
git restore .
```

Again, use this with extreme caution.

**Chapter 2 Summary:**

We've learned the foundational steps to use Git: verifying installation and configuring user identity (`git config`), starting new projects (`git init`), downloading existing ones (`git clone`), and the core workflow of tracking changes: checking status (`git status`), staging changes (`git add`), saving snapshots (`git commit`), viewing history (`git log`), and interacting with files (modifying, deleting, unstaging, discarding changes).

We now have a solid understanding of managing a project's history locally with Git. The next step is to see how GitHub fits into this picture and how we can use it to host our repository and prepare for collaboration.