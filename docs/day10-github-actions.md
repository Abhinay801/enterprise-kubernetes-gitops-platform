Objective

The objective of this project is to understand Continuous Integration (CI) using GitHub Actions. GitHub Actions automatically runs workflows whenever code is pushed to a GitHub repository, helping developers validate, test, and maintain application quality before deployment.

What is CI?

CI (Continuous Integration) is the practice of automatically checking code whenever developers push changes to a repository.

Instead of manually testing every change, CI automatically:

Downloads the latest code.
Runs validation or tests.
Reports whether the changes are successful or failed.
Without CI
Developer
     │
Push Code
     │
     ▼
Manual Testing
     │
     ▼
Deploy

Problems:

Time-consuming
Human errors
Bugs may be missed
With CI
Developer
     │
Push Code
     │
     ▼
GitHub Actions
     │
     ├── Validate Code
     ├── Run Tests
     ├── Check Helm Chart
     └── Report Result

Benefits:

Automatic validation
Faster feedback
Fewer errors
Better code quality
What is CD?

CD (Continuous Delivery/Continuous Deployment) is the next step after CI.

There are two common meanings:

Continuous Delivery

The application is automatically prepared for deployment, but a person decides when to deploy it.

Code
 │
 ▼
CI
 │
 ▼
Ready to Deploy
 │
 ▼
Manual Approval
 │
 ▼
Production
Continuous Deployment

The application is automatically deployed after all checks pass.

Code
 │
 ▼
CI
 │
 ▼
Tests Passed
 │
 ▼
Automatically Deploy
 │
 ▼
Production
Difference Between CI and CD
CI	CD
Validates code	Deploys application
Finds bugs early	Automates release process
Runs tests and checks	Delivers software to users
Triggered by code changes	Triggered after successful CI
Why Use GitHub Actions?

GitHub Actions is GitHub's built-in automation platform.

It helps you:

Automatically run workflows on every push.
Validate Kubernetes YAML.
Lint Helm charts.
Run tests.
Build Docker images.
Deploy applications.
Save time by automating repetitive tasks.

Instead of running commands manually, GitHub Actions runs them for you.

Workflow Structure

Every GitHub Actions workflow follows this structure:

Workflow
   │
   ▼
Jobs
   │
   ▼
Steps
   │
   ▼
Commands or Actions
Example Architecture
GitHub Repository
        │
Push Code
        │
        ▼
GitHub Actions Workflow
        │
        ▼
Job
        │
        ▼
Step 1 → Checkout Code
Step 2 → Install Helm
Step 3 → Helm Lint
Step 4 → Validate YAML
Step 5 → Success Message
What is a Workflow?

A workflow is the complete automation process defined in a YAML file.

Example:

name: Kubernetes CI

A workflow contains:

Triggers
Jobs
Steps
What are Jobs?

A job is a group of related tasks.

Example:

jobs:
  validate:

A workflow can contain multiple jobs.

Example:

Workflow
   │
   ├── Build Job
   ├── Test Job
   ├── Validate Job
   └── Deploy Job

Jobs can run in parallel or one after another, depending on the workflow.

What are Steps?

A step is an individual task inside a job.

Example:

steps:
- uses: actions/checkout@v4

- uses: azure/setup-helm@v4

- run: helm lint .

Each step performs one specific action.

Example:

Validate Job
      │
      ├── Checkout Repository
      ├── Install Helm
      ├── Verify Helm
      ├── Helm Lint
      └── Success Message
What are Runners?

A runner is the machine that executes your workflow.

Example:

runs-on: ubuntu-latest

GitHub creates a temporary Ubuntu virtual machine.

GitHub
     │
Creates Ubuntu VM
     │
     ▼
Runs Workflow
     │
Deletes VM After Completion

You can also use:

runs-on: windows-latest

or

runs-on: macos-latest
Complete CI Architecture
Developer
     │
     ▼
Push Code to GitHub
     │
     ▼
GitHub Repository
     │
     ▼
Workflow Triggered
     │
     ▼
Runner (Ubuntu VM)
     │
     ▼
Checkout Repository
     │
     ▼
Install Helm
     │
     ▼
Verify Helm
     │
     ▼
Helm Lint
     │
     ▼
Validate Kubernetes Files
     │
     ▼
Workflow Success
Commands Used
Check Helm Version
helm version
Lint Helm Chart
helm lint .
List Kubernetes YAML Files
find kubernetes -name "*.yaml" -o -name "*.yml"
View Workflow Runs

Go to:

GitHub Repository → Actions

This page shows:

Workflow history
Success or failure status
Logs for every job and step
Screenshots to Include
GitHub Actions workflow page
Successful workflow run
Helm lint output
Workflow logs
Success status
Interview Questions
1. What is CI?

Continuous Integration (CI) is the practice of automatically validating and testing code whenever changes are pushed to a repository. It helps detect issues early and improves code quality.

2. What is CD?

Continuous Delivery (or Continuous Deployment) extends CI by automating the release process. Continuous Delivery prepares the application for deployment, while Continuous Deployment automatically releases it after all checks pass.

3. Why use GitHub Actions?

GitHub Actions automates development workflows such as code validation, testing, building, and deployment. It integrates directly with GitHub repositories and reduces manual effort.

4. What is a Workflow?

A workflow is a YAML-defined automation process that specifies when automation should run and what tasks it should perform.

5. What is a Job?

A job is a collection of related steps executed on the same runner. A workflow can contain one or more jobs.

6. What is a Step?

A step is an individual task within a job. It can execute a shell command (run) or use a reusable GitHub Action (uses).

7. What is a Runner?

A runner is the machine that executes the workflow. GitHub-hosted runners provide temporary virtual machines (such as Ubuntu, Windows, or macOS), and self-hosted runners can also be configured by organizations for custom environments.
