## Chapter 7: Automating Workflows with GitHub

In modern software development, automation is key to increasing efficiency, improving code quality, and ensuring rapid, reliable deployments. GitHub provides a powerful integrated platform for this: **GitHub Actions**. GitHub Actions allows you to automate tasks directly within your repository based on various events.

### 7.1 Introduction to GitHub Actions

#### 7.1.1 What are GitHub Actions?

GitHub Actions is a **CI/CD (Continuous Integration/Continuous Deployment) and automation platform** provided by GitHub. It allows you to create custom workflows directly in your GitHub repository to build, test, and deploy your code, as well as automate almost any other task you can imagine.

Instead of relying on external CI servers (like Jenkins, Travis CI, etc., which were common before integrated solutions), you can configure and run your automation directly within the GitHub ecosystem. This simplifies setup and integrates tightly with other GitHub features like Pull Requests and Issues.

#### 7.1.2 Core Concepts (Workflows, Events, Jobs, Steps, Runners)

Understanding these core concepts is essential for working with GitHub Actions:

* **Workflow:** A customizable automated process that you set up in your repository. A workflow is defined by a YAML file (`.yml` or `.yaml`) placed in the `.github/workflows` directory in your repository's root. A repository can have multiple workflows.
* **Event:** A specific activity in your repository that triggers a workflow run. Examples include pushing code (`push`), creating a pull request (`pull_request`), opening an issue (`issues`), releasing code (`release`), or even on a schedule (`schedule`).
* **Job:** A set of steps that execute on the same runner. Workflows can have multiple jobs, which can run sequentially or in parallel. Jobs are independent of each other by default, but you can define dependencies.
* **Step:** An individual task within a job. A step can be: 
  * Running a command-line script (e.g., `run: npm install`).
  * Running an **Action** (reusable pieces of code shared on GitHub Marketplace).
* **Action:** A custom application for the GitHub Actions platform that performs a complex task. You can write your own Actions or use Actions created by the community or GitHub. Examples include checking out your code (`actions/checkout`), setting up a specific programming language environment (`actions/setup-node`), or publishing packages.
* **Runner:** A server that runs your workflow jobs. GitHub provides **GitHub-hosted runners** with various operating systems (Ubuntu, Windows, macOS) and pre-installed software. You can also set up **self-hosted runners** on your own infrastructure for more control or specific environments.

**[Insert Diagram: GitHub Actions Workflow Structure - Showing Event -> Workflow (.yml) -> Jobs (parallel/sequential) -> Steps (run commands, use Actions) -> Runner]**

### 7.2 Creating Your First Workflow

Let's create a simple workflow that runs whenever code is pushed to the `main` branch. This workflow will simply print a message.

#### 7.2.1 Workflow Syntax (YAML)

Workflows are defined in YAML files. YAML is a human-readable data serialization format, often used for configuration files.

Key YAML syntax features:

* Indentation defines structure (use spaces, not tabs).
* Lists start with `-`.
* Key-value pairs use `:`.

Let's create the directory and file for our first workflow in your local repository (`my-first-git-project`).

```bash
mkdir .github
mkdir .github/workflows
# Use your preferred text editor to create the file
# On macOS/Linux:
# nano .github/workflows/hello-world.yml
# On Windows:
# notepad .github\workflows\hello-world.yml
```

Now, add the following content to `.github/workflows/hello-world.yml`:

```yaml
name: Hello World Workflow

on:
  push:
    branches:
      - main # This workflow runs on pushes to the 'main' branch

jobs:
  greet: # This is the ID of the job
    runs-on: ubuntu-latest # Specify the runner environment

    steps:
      - name: Print a greeting
        run: echo "Hello, GitHub Actions!"
```

Let's break down this YAML:

* `name`: The name of the workflow, displayed on the Actions tab in GitHub.
* `on`: Specifies the event(s) that trigger this workflow. Here, it's a `push` event, limited to the `main` branch.
* `jobs`: Defines the jobs in the workflow. This workflow has one job with the ID `greet`.
* `runs-on`: Specifies the type of runner the job will run on. `ubuntu-latest` is a common GitHub-hosted runner.
* `steps`: A sequence of steps within the `greet` job.
* `- name`: A human-readable name for the step, shown in the workflow logs.
* `run`: Executes a command-line script.

#### 7.2.2 Setting up a Simple CI Workflow (e.g., running tests)

A common use for GitHub Actions is Continuous Integration (CI), such as automatically running tests whenever changes are pushed or a Pull Request is opened.

Let's create a workflow that checks out the code and runs a dummy command to simulate running tests. We'll use a popular Action: `actions/checkout@v4` to get our code onto the runner.

Create a new file: `.github/workflows/ci-checks.yml`

```yaml
name: CI Checks

on:
  push:
    branches:
      - main
      - develop # Also run on develop branch (if you use one)
  pull_request:
    branches:
      - main
      - develop # Run when a PR is opened targeting main or develop

jobs:
  build_and_test: # Job ID
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4 # Use the checkout Action

      - name: Set up Node.js (example for a JS project)
        uses: actions/setup-node@v4
        with:
          node-version: '20' # Specify Node.js version

      - name: Install dependencies (example)
        run: npm install

      - name: Run tests (example)
        run: npm test
        # Or if you have a different test command, e.g.,:
        # run: make test
        # run: pytest
        # run: go test ./...

      - name: Perform build (example)
        run: npm run build
        # run: make build
```

This workflow:

* Runs on `push` to `main` or `develop`, or on `pull_request` targeting `main` or `develop`.
* Has one job `build_and_test` running on an Ubuntu runner.
* The first step uses the `actions/checkout@v4` Action to get a copy of your repository onto the runner.
* The second step uses the `actions/setup-node@v4` Action to install a specific Node.js version (replace with your language's setup action if needed, e.g., `setup-python`, `setup-java`, `setup-go`, `setup-dotnet`).
* The subsequent steps run shell commands to install project dependencies, run tests, and perform a build. If any `run` command exits with a non-zero status, the job (and thus the workflow run) will fail.

Now, commit these workflow files and push them to your GitHub repository:

```bash
git add .github/workflows/hello-world.yml .github/workflows/ci-checks.yml
git commit -m "Add GitHub Actions workflows for hello world and CI checks"
git push origin main # Assuming you are on main and origin is your GitHub repo
```

Go to the "Actions" tab on your GitHub repository page. You should see a new workflow run triggered by your push. Click on the run to see the jobs and steps, and view the logs.

**[Insert Screenshot: GitHub Actions tab showing workflow runs]**
**[Insert Screenshot: GitHub Actions run details showing jobs and steps]**

### 7.3 Continuous Integration (CI)

As shown in the `ci-checks.yml` example, automating builds and tests is a core CI practice.

#### 7.3.1 Automating Builds and Tests on Push/Pull Request

By configuring workflows to trigger on `push` and `pull_request` events, you ensure that:

* Every time code is pushed to a development branch, automated checks run.
* Every time a Pull Request is opened (proposing changes to a main branch), automated checks run on the proposed changes *before* they are merged.

This immediate feedback loop helps catch errors early, reducing the chances of breaking the main branch.

#### 7.3.2 Status Checks and Branch Protection

GitHub integrates the results of GitHub Actions workflow runs directly into the Pull Request interface. If a workflow is configured to run on `pull_request`, its status (pending, success, failure) will be displayed on the PR page.

You can leverage this with **Branch Protection Rules**. Found in the repository **Settings > Branches**, these rules allow you to enforce policies on specific branches (like `main` or `develop`).

Key Branch Protection Rule settings include:

* **Require status checks to pass before merging:** Prevents merging a Pull Request until specific GitHub Actions workflows (configured as "status checks") have completed successfully. This is essential for CI – you can require tests, linting, or build checks to pass before code can be merged into a protected branch. **[Insert Screenshot: GitHub Branch Protection Rules - Requiring status checks]**
* **Require pull request reviews before merging:** Enforces that a minimum number of approving reviews are needed.
* **Require signed commits:** Ensures all commits on the branch are cryptographically signed.
* **Require linear history:** Prevents merge commits, enforcing that merges must be done using squash or rebase strategies.
* **Include administrators:** Apply the rules to repository administrators as well.

By setting up branch protection on your `main` branch (and potentially other critical branches), you can significantly increase the reliability and stability of your codebase by ensuring automated checks and code reviews are completed before changes are integrated.

### 7.4 Continuous Deployment (CD) (Introduction)

Beyond just building and testing (CI), GitHub Actions can automate the deployment of your application (CD).

#### 7.4.1 Deploying to Different Environments

You can create workflows that trigger deployment steps when code is merged into a specific branch (e.g., merge to `main` deploys to staging, merge to `release` deploys to production) or when a new release is published on GitHub.

Deployment steps could involve:

* Building your application.
* Packaging it.
* Connecting to a cloud provider (AWS, Azure, GCP).
* Using SSH to connect to a server.
* Using deployment tools (like Docker, Kubernetes, serverless functions).

Example workflow trigger for deployment on release:

```yaml
on:
  release:
    types: [published] # Trigger when a release is published on GitHub
```

Or for deployment on push to a specific branch:

```yaml
on:
  push:
    branches:
      - production # Trigger when code is pushed/merged to the 'production' branch
```

Deployment workflows often require securely accessing external services.

#### 7.4.2 Using Secrets

Deployment often requires sensitive information like API keys, passwords, or cloud provider credentials. Storing these directly in your workflow file or repository is insecure.

GitHub **Secrets** provide a secure way to store sensitive information.

* **How it works:** Go to your repository **Settings > Secrets and variables > Actions**. You can add repository secrets with key-value pairs. These secrets are encrypted and only exposed to your GitHub Actions workflows as environment variables during a workflow run. They are not visible in the workflow file or logs. **[Insert Screenshot: GitHub Repository Secrets settings page]**
* **Using Secrets in Workflows:** Access secrets using the `secrets` context in your workflow file.

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      # ... setup steps ...
      - name: Deploy to server
        run: |
          # Example: Using an SSH key secret
          echo "${{ secrets.SSH_PRIVATE_KEY }}" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh user@your-server.com "deploy-script.sh"
        env:
          # Example: Passing an API key as an environment variable
          API_KEY: ${{ secrets.MY_API_KEY }}
```

Using secrets is crucial for secure CI/CD pipelines.

### 7.5 Other Automation Possibilities

GitHub Actions can automate much more than just CI/CD:

* **Linting and Formatting Checks:** Automatically check code style and syntax on every commit or PR.
* **Auto-labeling Issues/PRs:** Use workflow to apply labels to issues or PRs based on their title, content, or author.
* **Sending Notifications:** Notify teams in Slack, Discord, or other platforms about workflow status changes.
* **Generating Documentation:** Automatically build and publish documentation sites when changes are merged.
* **Managing Issues and Pull Requests:** Workflows can automatically close stale issues or assign reviewers to PRs.

Explore the GitHub Marketplace for a vast collection of pre-built Actions to perform various tasks.

**Chapter 7 Summary:**

We introduced GitHub Actions as GitHub's integrated automation platform, covering its core concepts: Workflows, Events, Jobs, Steps, Actions, and Runners. We created a simple workflow to run a command and a more practical CI workflow using standard Actions for checking out code and setting up an environment, simulating build and test steps. We discussed how GitHub Actions integrates with Pull Requests as status checks and how to enforce these checks using Branch Protection Rules for robust CI. We also briefly touched upon Continuous Deployment (CD) and the importance of securely managing sensitive information using GitHub Secrets. Finally, we highlighted other automation possibilities with GitHub Actions beyond CI/CD.

You now have the foundation to start automating tasks in your GitHub repositories, leading to more efficient and reliable development workflows.