## Appendix A: Glossary of Git and GitHub Terms

This glossary defines key terms used throughout this textbook, providing quick refreshers for concepts related to Git and GitHub.

**Action:** A custom application for the GitHub Actions platform that performs a specific task in a workflow (e.g., checking out code, setting up an environment).

**Annotated Tag:** A Git tag that is stored as a full object in the Git database. It contains a tagger name, email, date, and message, and is recommended for marking releases.

**Approval (Pull Request):** A formal positive review on a Pull Request, indicating that the reviewer agrees with the proposed changes and they are ready to be merged (often required by Branch Protection Rules).

**Bare Repository:** A Git repository that does *not* have a working directory. It contains only the `.git` directory content. Often used as a central remote repository.

**Base Branch:** In a Pull Request, the branch into which the changes from the compare branch are proposed to be merged (e.g., `main`).

**Blame (**`git blame`**):** A Git command that shows the author, timestamp, and commit hash of the last change to each line of a file. Useful for tracking down when and why specific lines were modified.

**Branch:** A lightweight, movable pointer to a commit in Git. Represents an independent line of development.

**Branch Protection Rules:** Settings on GitHub (or other Git hosting) that enforce policies on specific branches, such as requiring status checks, pull request reviews, or preventing force pushes.

**Checkout (**`git checkout`**,** `git switch`**):** Git commands used to switch between branches or restore working directory files to a specific commit's state. `git switch` is the newer command specifically for changing branches.

**CI/CD:** Continuous Integration / Continuous Deployment (or Continuous Delivery). Automated practices using tools like GitHub Actions to build, test, and deploy code frequently and reliably.

**Clone (**`git clone`**):** A Git command to create a local copy of an existing Git repository, including its entire history. Automatically sets up an `origin` remote.

**Code Review:** The process of having other developers examine proposed code changes, typically done via Pull Requests on platforms like GitHub.

**Commit:** A snapshot of your repository at a specific point in time. The fundamental unit of work in Git. Each commit has a unique hash, a message, and points to its parent commit(s).

**Commit Message:** A description included with a commit, explaining *what* changes were made and *why*. Good commit messages are crucial for understanding history.

**Compare Branch:** In a Pull Request, the branch containing the changes being proposed for inclusion into the base branch.

**Conflict (Merge Conflict):** A situation where Git cannot automatically combine changes during a merge or rebase because the same lines were modified differently, or one version deleted a file the other modified. Requires manual resolution.

**Contributor:** A person who has made contributions to a repository, typically via commits merged through Pull Requests.

**Default Branch:** The primary branch of a repository (usually `main` or `master`). It's the branch Git checks out by default after cloning and often represents the stable version of the project.

**Dependabot:** A GitHub feature that scans dependencies for known vulnerabilities and can create automated Pull Requests to update them.

**Detached HEAD:** A state in Git where `HEAD` points directly to a specific commit instead of a branch pointer. New commits made in this state do not belong to a branch and can be lost unless a new branch is created.

**Diff (**`git diff`**):** A Git command that shows the differences between different points in the repository (e.g., working directory vs. staging area, staged vs. last commit, between commits or branches).

**Distributed Version Control System (DVCS):** A type of VCS where every user has a full copy of the repository, including its entire history (e.g., Git, Mercurial).

**Fork:** A copy of a repository under a different user or organization's account on a platform like GitHub. Often used in open-source workflows to allow contributions without direct write access to the original repository.

**Fetch (**`git fetch`**):** A Git command that downloads commits, files, and references from a remote repository to your local repository, but does *not* automatically merge them into your current branch.

**Git:** A free and open-source Distributed Version Control System. The underlying technology for tracking changes.

**GitHub:** A web-based platform for hosting Git repositories and providing collaboration, project management, and automation tools built around Git.

**HEAD:** A pointer in Git that typically points to the tip of the current branch. It represents the current snapshot you are working on. In a detached HEAD state, it points directly to a commit.

**Ignored Files (.gitignore):** Files and directories that Git is configured to ignore and not track changes for, specified in a `.gitignore` file.

**Initialize (**`git init`**):** A Git command to create a new, empty Git repository in the current directory.

**Issue:** A feature on GitHub (and other platforms) used to track tasks, bugs, feature requests, or other items related to a project.

**Job:** A set of steps in a GitHub Actions workflow that executes on a single runner.

**Lightweight Tag:** A simple pointer to a commit in Git, without extra metadata like a message or tagger information.

**Main Branch:** The conventional name for the primary development branch in a Git repository (formerly `master`). Often represents the stable or production-ready code.

**Markdown (.md):** A lightweight markup language commonly used for README files, wiki pages, and comments on GitHub.

**Merge (**`git merge`**):** The process of combining changes from one Git branch into another. Can result in a fast-forward merge or a three-way merge with a merge commit.

**Merge Conflict:** See **Conflict**.

**Merge Commit:** A commit created during a three-way merge that ties together the histories of the two branches being merged.

**Origin:** The conventional name for the default remote repository that a local repository was cloned from or linked to.

**Private Repository:** A repository visible only to the owner and explicitly added collaborators/teams.

**Public Repository:** A repository visible to anyone on the internet.

**Pull (**`git pull`**):** A Git command that is a combination of `git fetch` followed by `git merge`. It downloads changes from a remote repository and automatically integrates them into the current local branch.

**Pull Request (PR):** A feature on GitHub (and similar platforms) used to propose changes made on a branch, facilitate code review, and integrate those changes into another branch (typically the base branch).

**Push (**`git push`**):** A Git command to upload local commits and branch references to a remote repository.

**Rebase (**`git rebase`**):** A Git command to move or combine a sequence of commits to a new base commit. It rewrites commit history and creates a linear history.

**Reflog (**`git reflog`**):** A local log that tracks updates to the tips of branches and other references in the repository. Useful for recovering lost commits.

**Remote:** A reference to another copy of the repository, usually hosted on a server (like GitHub). Allows pushing and pulling changes.

**Repository (Repo):** The central storage location for a project's files and the entire history of changes managed by Git. Contains the hidden `.git` directory.

**Revert (**`git revert`**):** A Git command that creates a *new* commit to undo the changes introduced by a specific previous commit. Does *not* rewrite history, making it safe for shared branches.

**Rewriting History:** Modifying past commits (e.g., using `git commit --amend`, `git rebase`, `git reset`). This changes commit hashes and should be avoided on branches that have been shared with others.

**Role:** Permission level assigned to a collaborator or team on a GitHub repository (Read, Triage, Write, Maintain, Admin).

**Runner:** The server environment where GitHub Actions workflow jobs execute (GitHub-hosted or self-hosted).

**Secrets:** Encrypted environment variables stored in GitHub repository or organization settings, used to securely provide sensitive information (like API keys) to GitHub Actions workflows.

**Snapshot:** How Git fundamentally stores data. Each commit is a snapshot of all the files in the project at that moment.

**Squash and Merge:** A Pull Request merge strategy on GitHub that combines all commits from the feature branch into a single new commit on the base branch.

**Staging Area (Index):** An intermediate area in Git where you collect changes (using `git add`) before committing them. It represents the snapshot that will be included in the next commit.

**Status (**`git status`**):** A Git command that shows the current state of the working directory and the staging area, indicating changes that are untracked, staged, or unstaged.

**Step:** An individual task within a job in a GitHub Actions workflow. Can be a command-line script or an Action.

**Tag:** A pointer in Git used to mark specific points in history as important, typically used for releases (e.g., v1.0).

**Three-Way Merge:** A type of merge that occurs when histories have diverged. Git uses the two branch tips and their common ancestor to create a new merge commit.

**Tracking Branch (Remote-tracking branch):** A reference in your local repository to the state of a branch on a remote repository (e.g., `origin/main`). Updated by `git fetch`.

**Untracked Files:** New files in your working directory that Git sees but are not yet being tracked by the repository.

**Upstream:** In a forking workflow, the original repository that was forked (in contrast to `origin`, which points to your fork).

**Version Control System (VCS):** A system that records changes to a file or set of files over time so that you can recall specific versions later.

**Webhook:** An automated message sent from GitHub to a configured URL in response to events in the repository. Used for integrating with external services.

**Working Directory:** The directory on your local file system that contains the project files you are currently working on.

**Workflow:** A customizable automated process defined in a YAML file in `.github/workflows` that runs in response to events in a GitHub repository.