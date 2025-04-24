## Chapter 8: Managing Permissions, Security, and Repository Settings

As projects grow and teams collaborate, controlling who has access to the codebase and ensuring its security become critical. GitHub provides a range of settings and features within each repository to manage access, protect branches, and identify potential security vulnerabilities.

### 8.1 Repository Visibility (Public, Private, Internal)

When creating a repository (as seen in Chapter 3), you choose its visibility. This setting dictates who can view the repository's content.

* **Public:** The repository is visible to everyone on the internet. People can view, fork, and clone the code. This is ideal for open-source projects, personal portfolios, or projects where transparency is desired. While anyone can *see* the code, only collaborators you explicitly add or contributors whose Pull Requests you merge can directly modify it.
* **Private:** Only you and the specific people or teams you grant access to can see and interact with the repository. This is the default for proprietary code, personal projects, or projects requiring strict access control.
* **Internal (GitHub Enterprise Cloud and Server):** If you are part of a GitHub Enterprise organization, you might have the "Internal" visibility option. This makes the repository visible to all members of your GitHub Enterprise organization but not to the general public on the internet. It's useful for sharing code within a company or large organization.

You can change a repository's visibility at any time in the repository **Settings > General**.

### 8.2 Collaborator Permissions

For Private repositories (and to control who can push to branches in Public ones), you explicitly add collaborators and define their permission levels. If the repository belongs to a GitHub Organization, you can also grant access to entire teams.

GitHub uses a role-based permission system. The common repository-level roles are:

* **Read:** Can view and clone the repository, open issues, and comment on issues and pull requests. Cannot push code.
* **Triage:** Can do everything a reader can, plus manage issues and pull requests (e.g., add labels, assignees, close issues) without write access to the code. Useful for project managers or community managers.
* **Write:** Can do everything a triager can, plus push non-protected branches, and contribute to and merge non-protected pull requests. This is a common role for developers.
* **Maintain:** Can do everything a writer can, plus manage repository settings (excluding sensitive ones), manage webhooks, and manage repository-level collaboration settings. Suitable for lead developers.
* **Admin:** Has full administrative access to the repository, including destructive actions like deleting the repository, managing sensitive settings, and managing all other users' access. Reserved for project owners or trusted administrators.

You add collaborators to a repository in **Settings > Access > Collaborators and teams**. For personal repositories, you add individual GitHub users. For organization repositories, you can add individuals or, more commonly, grant access to teams within the organization.

#### 8.2.1 Roles: Read, Triage, Write, Maintain, Admin

* **Read:** Basic access. Can view, clone, fork (if public), open issues, comment. Cannot push code or manage issues/PRs.
* **Triage:** Can manage issues and PRs (assign, label, close, etc.). Cannot push code.
* **Write:** The standard developer role. Can push to unprotected branches, create and merge PRs (if not blocked by protection rules).
* **Maintain:** Broader administrative access, but not full control. Can manage some settings, webhooks, etc.
* **Admin:** Full control, including critical settings, security features, and deleting the repository.

Choosing the appropriate permission level for individuals and teams is important for security and workflow efficiency. Grant the minimum level of access required for their role.

#### 8.2.2 Team Permissions (within Organizations)

If your repository is owned by a GitHub Organization (recommended for companies and large projects), you can create teams and grant permission levels to the entire team. This simplifies access management immensely. Instead of adding individual collaborators to repositories one by one, you add users to teams and grant the team access to repositories.

Organization owners can manage teams and their access in the Organization's settings.

### 8.3 Branch Protection Rules

Branch Protection Rules are one of the most important features for maintaining code quality and stability in shared repositories. They allow you to enforce policies on specific branches (like `main` or `develop`) to prevent direct pushes and require checks or reviews before changes are integrated.

You configure Branch Protection Rules in repository **Settings > Branches > Add branch protection rule**. You specify the branch name pattern (e.g., `main`, `develop`, `release/*`).

Key Protection Rules:

* **Require a pull request before merging:** Prevents anyone from pushing code directly to the protected branch. All changes must come through a Pull Request.
* **Require approvals:** Sets a minimum number of approving reviews needed on a Pull Request before it can be merged.
* **Dismiss stale pull request approvals when new commits are pushed:** If new commits are added to a PR after it has been approved, the approvals are dismissed, requiring reviewers to re-approve the latest changes.
* **Require status checks to pass before merging:** As seen in Chapter 7, this is crucial for CI. You can select specific GitHub Actions workflows (or other status checks configured via the API) that must pass before the PR can be merged.
* **Require branches to be up to date before merging:** Ensures the PR's branch is merged with the latest version of the base branch before merging is allowed. This helps prevent merge conflicts upon merging the PR.
* **Require signed commits:** Mandates that all commits on the protected branch are signed.
* **Include administrators:** Applies the protection rules to administrators. Highly recommended to prevent accidental direct pushes or merges.
* **Restrict who can push to matching branches:** Allows specifying which users, teams, or apps can push to the branch (even if it's a protected branch, they can push to it directly *if* allowed here and if PRs are not required, or push force if allowed).

Branch protection is fundamental to implementing robust workflows like the Feature Branch workflow and ensuring quality control.

### 8.4 Introduction to Repository Security Features

GitHub provides several built-in features to help you identify and address security vulnerabilities in your code and dependencies. Access these in the **Security** tab of your repository.

#### 8.4.1 Dependabot (Alerts, Security Updates)

* **Purpose:** Automatically scan your repository's dependencies for known security vulnerabilities.
* **How it works:** Dependabot reads your dependency manifest files (e.g., `package.json` for npm, `Gemfile` for RubyGems, `requirements.txt` for pip, etc.) and compares them against a database of known vulnerabilities (GitHub Advisory Database).
* **Alerts:** If a vulnerability is found in a dependency, Dependabot creates a **Dependabot alert** in the Security tab and notifies you. **[Insert Screenshot: GitHub Dependabot alerts list]**
* **Security Updates:** Dependabot can be configured to automatically create **pull requests** to update the vulnerable dependency to a safe version. These PRs include release notes and compatibility scores.

Enabling Dependabot helps you stay on top of known security risks introduced by your project's dependencies.

#### 8.4.2 Code Scanning (CodeQL)

* **Purpose:** Analyze your code for potential security vulnerabilities and coding errors.
* **How it works:** GitHub's default code scanning uses CodeQL, a powerful semantic code analysis engine. It treats code as data, allowing you to query it for vulnerabilities.
* **Integration:** Code scanning can be set up as a GitHub Actions workflow. It typically runs on `push` or `pull_request` events.
* **Alerts:** Findings appear as alerts in the Security tab and can also be displayed directly in the Pull Request interface on the "Files changed" tab.

Code scanning provides static analysis to find common security flaws like injection vulnerabilities, broken access control, or cryptographic issues directly in your source code.

#### 8.4.3 Secret Scanning

* **Purpose:** Detect secrets (like API keys, connection strings, private keys) that have been accidentally committed to your repository.
* **How it works:** GitHub scans for patterns that match known secret formats (tokens from cloud providers, popular services, etc.).
* **Alerts:** If a potential secret is found, GitHub creates a secret scanning alert and may notify the relevant service provider so they can revoke the compromised secret.

Secret scanning helps prevent potentially devastating security breaches caused by leaking credentials.

### 8.5 Managing Repository Settings

The **Settings** tab (accessible to users with Admin or Maintain roles) is the control panel for your repository. We've already touched on some sections like Access and Branches. Here's a look at other important settings:

#### 8.5.1 General Settings (Name, Description, Topics)

* **Repository Name:** Change the name of the repository. Note that changing the name redirects requests from the old URL for a while, but it's best to update any links.
* **Description:** Update the project's description.
* **Topics:** Add relevant topics (tags) to your repository. These help users discover your project on GitHub.
* **Features:** Enable or disable features like Issues, Pull Requests, Wiki, Projects, Discussions. You can turn off features you don't plan to use.
* **Archiving/Deleting:** Critical and irreversible actions. Archiving makes a repository read-only, useful for completed projects you want to preserve. Deleting permanently removes the repository.

#### 8.5.2 Managing Branches, Tags, and Releases

* **Branches:** Beyond protection rules, you can see and manage all branches, including deleting those that are no longer needed (though deleting via the command line or after merging a PR is more common).
* **Tags:** View and manage tags.
* **Releases:** Create and manage software releases. A release is tied to a Git tag and can include release notes and binary assets (like compiled executables or packaged code). Releases provide a historical record of release versions with associated files and documentation. **[Insert Screenshot: GitHub Releases page]**

#### 8.5.3 Webhooks

* **Purpose:** Integrate your GitHub repository with external services.
* **How it works:** Webhooks are automated messages sent from GitHub to a configured URL when specific events occur in your repository (e.g., a push, a pull request opened, an issue commented).
* **Use Case:** Triggering custom scripts on your server, notifying external systems, integrating with third-party CI/CD services (though GitHub Actions often replaces the need for webhooks for CI/CD integration within GitHub).

Webhooks are a flexible way to extend GitHub's functionality by reacting to events.

**Chapter 8 Summary:**

We covered essential administrative aspects of GitHub repositories. We discussed repository visibility (Public, Private, Internal) and how to manage collaborator permissions using different roles (Read, Triage, Write, Maintain, Admin) and teams in organizations. A major focus was placed on Branch Protection Rules as a critical tool for enforcing workflows and quality control by requiring PRs, reviews, and status checks. We introduced GitHub's built-in security features: Dependabot for dependency vulnerability scanning, Code Scanning for static code analysis, and Secret Scanning for detecting leaked credentials. Finally, we explored other important settings in the Settings tab, including general options, managing releases, and configuring webhooks for external integrations.

You now have a solid understanding of how to configure, secure, and manage access to your GitHub repositories, crucial for professional project management.