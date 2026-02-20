# Lab 1: CI/CD Fundamentals & Azure DevOps Overview

**Platform:** Azure DevOps
**Duration:** ~2 Hours
**Difficulty:** Beginner / Foundation

---

##  Lab Objectives

By the end of this lab, participants will be able to:

* Clearly differentiate **CI vs CD vs Release pipelines**
* Understand the **DevOps lifecycle and pipeline responsibilities**
* Navigate Azure DevOps services (Repos, Pipelines, Artifacts, Boards)
* Create an **Azure DevOps project**
* Import a **starter Git repository**
* Create and run a **first YAML pipeline**
* Inspect **pipeline execution, logs, and status**

---

##  Prerequisites

* Azure DevOps organization access
* GitHub account (for importing sample repo) *or* local Git installed
* Modern browser (Chrome / Edge)
* Basic Git concepts (clone, commit)

---

##  Lab Scenario (Real-World Context)

You’ve joined a team adopting **Azure DevOps for CI/CD**.
Your first task is to:

* Set up a project
* Onboard source code
* Create a basic CI pipeline
* Validate that code can be automatically built and verified


----

![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/architectures/media/azure-devops-ci-cd-architecture.svg?view=azure-devops)

![Image](https://media2.dev.to/dynamic/image/width%3D800%2Cheight%3D%2Cfit%3Dscale-down%2Cgravity%3Dauto%2Cformat%3Dauto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F4lgalznbjqx8vu9wx84v.png)

![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/media/troubleshooting/job-task-logs.png?view=azure-devops)

![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/troubleshooting/media/view-raw-log.png?view=azure-devops)

![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/troubleshooting/media/task-error-log.png?view=azure-devops)

----



This lab simulates **Day-1 DevOps onboarding**.

---

#  PART 1: CI/CD & Azure DevOps Concepts (Context Setup)

## Step 1: Understand CI vs CD vs Release

**Continuous Integration (CI)**

* Triggered on code commit
* Build, test, validate code
* Output = **artifact**

**Continuous Delivery (CD)**

* Moves artifact through environments
* Often manual approvals

**Release Pipelines**

* Older model (classic UI)
* YAML pipelines now preferred

💡 **Mental Model**

```
Code → Build (CI) → Artifact → Deploy (CD)
```

---

## Step 2: DevOps Lifecycle Mapping

Explain how pipelines support:

* Plan → Code → Build → Test → Release → Monitor

Pipeline responsibility:

* Developers push code
* Pipelines validate and package
* Ops deploy safely

---

# PART 2: Azure DevOps Platform Walkthrough

## Step 3: Access Azure DevOps

1. Open browser
 [https://dev.azure.com](https://dev.azure.com)
2. Sign in with Microsoft account
3. Select your **Azure DevOps Organization**

✅ You should see the **organization dashboard**

---

## Step 4: Azure DevOps Services Overview (Read-Only)

From left navigation, briefly open:

### Repos

* Git-based source control
* Branches, commits, PRs

### Pipelines

* CI/CD pipelines (YAML & classic)

### Artifacts

* Package & artifact feeds

### Boards (Context Only)

* Work items, backlogs, sprint tracking

💡 **Key Learning:**
CI/CD pipelines are **tightly integrated** with repos and artifacts.

---

#  PART 3: Create Azure DevOps Project

## Step 5: Create a New Project

1. Click **New Project**
2. Configure:

   * **Project name:** `ado-cicd-foundation`
   * **Visibility:** Private
   * **Version control:** Git
   * **Work item process:** Agile
3. Click **Create**

✅ Project is created successfully

---

## Step 6: Understand Project Structure

Inside the project, observe:

* Repos → empty Git repository
* Pipelines → no pipelines yet
* Artifacts → empty feed

💡 This is a **clean DevOps workspace**.

---

#  PART 4: Import / Clone Starter Repository

## Step 7: Import Sample Repository (Recommended)

1. Go to **Repos**
2. Click **Import**
3. Repository URL (example):

   ```
   https://github.com/microsoft/azure-devops-node-sample
   ```
4. Click **Import**

⏳ Wait for import to complete

---

### (Alternative – Local Git Clone)

```bash
git clone https://dev.azure.com/<org>/ado-cicd-foundation/_git/ado-cicd-foundation
```

---

## Step 8: Explore Repository Structure

Open repository and review:

* Application source code
* `package.json` / build files
* README

💡 **Key Learning:**
Pipelines always start from **code in repos**.

---

# PART 5: YAML Pipelines vs Classic Pipelines

## Step 9: Conceptual Comparison (Checkpoint)

| Classic Pipelines | YAML Pipelines     |
| ----------------- | ------------------ |
| UI-driven         | Code-driven        |
| Hard to version   | Version-controlled |
| Manual changes    | Git-tracked        |
| Legacy            | Recommended        |

 **We will use YAML pipelines only**

---

#  PART 6: Create Your First YAML Pipeline

## Step 10: Create Pipeline

1. Go to **Pipelines**
2. Click **Create Pipeline**
3. Select:

   * **Azure Repos Git**
   * Select your repository
4. Choose:

   * **Starter pipeline**

---

## Step 11: Review Starter YAML

Azure generates a default pipeline:

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- script: echo Hello, world!
  displayName: 'Run a one-line script'
```

💡 Explain:

* `trigger` → when pipeline runs
* `pool` → build agent
* `steps` → actions executed

---

## Step 12: Save & Run Pipeline

1. Click **Save and run**
2. Commit directly to `main`
3. Confirm run

⏳ Pipeline starts executing

---

#  PART 7: Pipeline Execution & Logs

## Step 13: Observe Pipeline Run

1. Open running pipeline
2. Observe:

   * Job status
   * Step execution
   * Agent allocation

---

## Step 14: Inspect Logs

1. Click **Run a one-line script**
2. View logs:

   ```
   Hello, world!
   ```

💡 **Learning Point:**
Logs are the **first place to debug pipeline failures**.

---

## Step 15: Pipeline Status & History

1. Navigate back to **Pipelines**
2. Observe:

   * Pipeline run history
   * Success / failure indicators
   * Commit that triggered run

---

#  PART 8: Basic Git + Pipeline Flow

## Step 16: Branching Strategy Overview (Conceptual)

Explain:

* `main` → stable branch
* `feature/*` → development work
* CI runs on commits / PRs

(No branching required in this lab)

---

#  PART 9: Validation & Wrap-Up

## Step 17: Validation Checklist

✔ Azure DevOps project created  
✔ Repository imported successfully  
✔ YAML pipeline created  
✔ Pipeline executed successfully  
✔ Logs inspected and understood  

---

##  Detailed Step-by-Step Execution Guide (Facilitator-Friendly)

This appendix expands each lab step into a **click-by-click guide** so participants can complete the lab without guessing.

---

### 1. Prerequisites – Validate Before Starting

1. **Azure DevOps organization**
   - Open a browser and go to `https://dev.azure.com`.
   - Sign in with your **Microsoft account**.
   - Verify that you see an **organization name** in the top-left (for example, `your-org-name`).
   - If you do **not** have an organization, use the portal wizard to **create a new organization**.
2. **Permissions**
   - Make sure your account can **create projects and pipelines** in the organization.
   - If you cannot create a project, contact an **Organization Administrator**.
3. **GitHub access**
   - Open `https://github.com/microsoft/azure-devops-node-sample` to confirm it is reachable.
4. **Browser**
   - Use a modern browser such as **Microsoft Edge** or **Google Chrome**.
   - Incognito / InPrivate mode can help avoid cached authentication issues in shared environments.

---

### 2. Access Azure DevOps & Create the Project

1. Go to `https://dev.azure.com` in your browser and sign in.
2. At the top-left, confirm you are in the **correct organization** (switch if needed by using your profile menu).
3. On the organization home page, click **New project**.
4. Fill in:
   - **Project name:** `ado-cicd-foundation`
   - **Description:** optional, for example "CI/CD foundation lab".
   - **Visibility:** **Private**
   - **Work item process:** **Agile**
   - **Version control:** **Git**
5. Click **Create**.
6. Wait to be redirected to the **project home** for `ado-cicd-foundation`.

✅ **Validation:** Left navigation shows `Overview`, `Boards`, `Repos`, `Pipelines`, and `Artifacts` for this project.

---

### 3. Confirm Initial Project State

1. In the left menu, click **Repos → Files**.
2. Confirm that:
   - The repo is **empty** (you should see an “Initialize” or “Import” option).
3. In the left menu, click **Pipelines → Pipelines**.
   - Confirm there are **no pipelines** yet.
4. In the left menu, click **Artifacts**.
   - Confirm there are **no feeds** or packages yet.

This confirms you are starting from a **clean DevOps workspace** for the lab.

---

### 4. Import the Sample Git Repository

1. Go to **Repos → Files**.
2. If the repo is empty, click **Import** (or use the ⋮ menu → **Import repository**).
3. In **Repository type**, leave the default **Git**.
4. In **Clone URL**, paste:

   ```
   https://github.com/microsoft/azure-devops-node-sample
   ```

5. Click **Import** and wait for the operation to complete.
6. When done, the file tree should populate with folders and files from the sample repo.
7. Look at the **branch dropdown** at the top of the Repos view:
   - Note whether the default branch is named **`main`** or **`master`** (you will need this for the YAML trigger).

✅ **Validation:** You can open files like `package.json` and see valid content (Node.js app sample).

---

### 5. Create the First YAML Pipeline

1. In the left menu, go to **Pipelines → Pipelines**.
2. Click **Create Pipeline** (or **New pipeline** if one already exists).
3. When asked “Where is your code?”, choose **Azure Repos Git**.
4. Select the repository belonging to `ado-cicd-foundation` (the one you just imported into).
5. On the “Configure your pipeline” screen, choose **Starter pipeline**.
6. Azure DevOps opens the YAML editor with a **default starter pipeline**.

You should see YAML similar to this:

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- script: echo Hello, world!
  displayName: 'Run a one-line script'
```

---

### 6. Adjust the YAML Trigger (Branch Name)

1. In another tab, go back to **Repos → Files** and verify the **default branch name**:
   - If the branch dropdown shows **`main`**, you can keep `trigger: - main` in the YAML.
   - If it shows **`master`**, change the YAML to:

     ```yaml
     trigger:
     - master
     ```

2. Do **not** change `pool` or `steps` for this introductory lab unless instructed.

✅ **Validation:** The YAML `trigger` branch matches the actual default branch in Repos.

---

### 7. Save and Run the Pipeline

1. In the YAML editor, click **Save and run** (top right).
2. In the dialog:
   - Leave **“Commit directly to the \<branch\>”** selected (this will be `main` or `master`).
   - Optionally, enter a commit message like `Add starter pipeline`.
3. Click **Save and run** again.
4. Azure DevOps will:
   - Commit the YAML file to the repo.
   - Create the pipeline definition.
   - Start the **first run** of this pipeline automatically.

You will be redirected to the run details page.

---

### 8. Observe the Pipeline Run and Logs

1. On the run details page, watch the **job** (for example `Job`) move from **Queued** → **Running** → **Succeeded**.
2. Click on the **job name** to see individual steps:
   - `Initialize job`
   - `Checkout`
   - `Run a one-line script`
   - `Finalize job`
3. Click the **Run a one-line script** step.
4. In the log output, confirm you see:

   ```
   echo Hello, world!
   Hello, world!
   ```

✅ **Validation:** The job and all steps show **Succeeded**, and the script output includes `Hello, world!`.

---

### 9. Review Pipeline History and CI Flow

1. Navigate to **Pipelines → Pipelines**.
2. You should now see your pipeline listed with:
   - A **green check** (Succeeded) for the latest run.
   - The **branch** and **commit message** that triggered it.
3. Click the pipeline name to open the **Runs** tab.
4. Observe that:
   - Each run is tied to a specific **commit**.
   - Clicking a run shows the same logs you previously inspected.

This demonstrates the basic **CI loop**: commit → pipeline → logs → status.

---

### 10. Common Issues & Quick Troubleshooting

- **Pipeline never triggers on future commits**
  - Confirm the YAML `trigger` branch matches the branch you are committing to (`main` vs `master`).
  - Ensure the YAML file is in the **root** of the repo (default behavior for the starter pipeline).
- **No permission to create project or pipeline**
  - Ask an org admin to add you to the appropriate **security groups** (for example, Project Contributors) and allow pipeline creation.
- **Import option not visible in Repos**
  - There may already be content in the repo:
    - Use the repo **⋮ menu → Import repository**, or
    - Create a **new repository** in Project Settings and import into that.

---

##  Key Takeaways

* CI/CD automates validation and delivery
* Azure DevOps provides **end-to-end CI/CD tooling**
* YAML pipelines are **code, not configuration**
* Pipelines run on hosted agents
* Logs are critical for troubleshooting
* Every pipeline starts with **source code**

---

##  Session Outcome

✅ Participants understand **how CI/CD works in Azure DevOps**
✅ Participants can **create and run a basic YAML pipeline**
✅ Participants are ready to **author real build pipelines**

---
