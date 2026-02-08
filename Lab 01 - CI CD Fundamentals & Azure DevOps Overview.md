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
