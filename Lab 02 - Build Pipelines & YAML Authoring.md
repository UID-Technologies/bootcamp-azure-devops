#  Lab 2: Build Pipelines & YAML Authoring (Azure DevOps)

**Platform:** Azure DevOps
**Duration:** ~2 Hours
**Level:** Foundation (Hands-on heavy)

---

##  Lab Objectives

By the end of this lab, participants will be able to:

* Understand **YAML pipeline structure**
* Configure **triggers and variables**
* Define **build jobs and steps**
* Generate and publish **build artifacts**
* Read and troubleshoot **pipeline logs**
* Refactor YAML for **clarity and maintainability**

---

## Prerequisites

* Completed **Session 1 lab**
* Azure DevOps project: `ado-cicd-foundation`
* Imported starter repository
* One successful YAML pipeline run
* Basic YAML syntax familiarity

---

## Lab Scenario (Real-World Context)

You are now responsible for **building application artifacts** automatically.
Your pipeline must:

* Trigger on code changes
* Run a repeatable build
* Produce artifacts for later deployment
* Be readable and maintainable by the team

---

![Image](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/images/key-concepts-overview.png)

![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/architectures/media/azure-devops-ci-cd-architecture.svg?view=azure-devops)

![Image](https://stacksimplify.com/course-images/azure-devops-pipelines-key-concepts-1.png)

![Image](https://miro.medium.com/1%2ARZK-XREfjuBnRYQ29cwMCw.jpeg)

![Image](https://static.wixstatic.com/media/14a6f5_3843bf9397a5471cb9e629acd05138e0~mv2.png/v1/fill/w_1000%2Ch_453%2Cal_c%2Clg_1%2Cq_90/14a6f5_3843bf9397a5471cb9e629acd05138e0~mv2.png)


---

#  PART 1: YAML Pipeline Anatomy (Foundation)

## Step 1: Review YAML Pipeline Structure (Checkpoint)

A typical build pipeline:

```yaml
trigger
pool
variables
stages
  jobs
    steps
```

💡 **Mental Model**

* Pipeline = *what*
* Job = *where*
* Step = *how*

Trainer checkpoint ✔️

---

# PART 2: Add Triggers & Variables

## Step 2: Open Existing Pipeline YAML

1. Go to **Pipelines**
2. Open your pipeline
3. Click **Edit**

---

## Step 3: Configure Branch Trigger

Update YAML:

```yaml
trigger:
  branches:
    include:
      - main
```

💡 Pipeline runs automatically on commits to `main`.

---

## Step 4: Add Pipeline Variables

Add variables section:

```yaml
variables:
  buildConfiguration: 'Release'
  artifactName: 'drop'
```

💡 Variables improve **reusability and readability**.

---

# PART 3: Configure Build Job & Steps

## Step 5: Define Agent Pool

```yaml
pool:
  vmImage: 'ubuntu-latest'
```

Explain:

* Microsoft-hosted agent
* Clean environment every run

---

## Step 6: Add Build Steps (Example – Node.js App)

Replace steps with:

```yaml
steps:
- task: NodeTool@0
  inputs:
    versionSpec: '18.x'
  displayName: 'Install Node.js'

- script: |
    npm install
    npm run build
  displayName: 'Install dependencies and build app'
```

💡 Steps are **executed sequentially**.

---

## Step 7: Save & Run Pipeline

1. Click **Save**
2. Commit changes to `main`
3. Run pipeline

✅ Pipeline executes build steps

---

# PART 4: Inspect Build Logs

## Step 8: Review Job & Step Logs

1. Open pipeline run
2. Click each step:

   * Node install
   * Build execution
3. Observe:

   * Execution time
   * Success/failure
   * Console output

💡 Logs are the **primary debugging tool**.

---

# PART 5: Publish Build Artifacts

## Step 9: Add Artifact Publish Step

Append to YAML:

```yaml
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)'
    ArtifactName: '$(artifactName)'
    publishLocation: 'Container'
  displayName: 'Publish build artifacts'
```

💡 Artifacts = output of CI pipeline.

---

## Step 10: Run Pipeline & Verify Artifacts

1. Save & run pipeline
2. Open completed run
3. Click **Artifacts**

✅ Artifact `drop` is available

---

#  PART 6: Refactor YAML for Readability

## Step 11: Clean & Structured YAML (Final Version)

Example final pipeline:

```yaml
trigger:
  branches:
    include:
      - main

variables:
  buildConfiguration: 'Release'
  artifactName: 'drop'

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: NodeTool@0
  displayName: 'Install Node.js'
  inputs:
    versionSpec: '18.x'

- script: |
    npm install
    npm run build
  displayName: 'Build application'

- task: PublishBuildArtifacts@1
  displayName: 'Publish build artifacts'
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)'
    ArtifactName: '$(artifactName)'
```

💡 Clean YAML = easier reviews + fewer bugs.

---

#  PART 7: Failure & Troubleshooting (Optional Exercise)

## Step 12: Introduce a Failure (Optional)

Change build command to:

```bash
npm run build-fail
```

Run pipeline → observe failure → inspect logs → revert change.

💡 Failure handling builds **real DevOps confidence**.

---

#  PART 8: Validation & Wrap-Up

## Step 13: Validation Checklist

✔ Trigger configured
✔ Variables defined
✔ Build job created
✔ Build steps executed
✔ Artifact published
✔ Logs inspected
✔ YAML refactored

---

##  Key Takeaways

* YAML pipelines are **code-first CI**
* Variables reduce duplication
* Hosted agents provide clean builds
* Artifacts are the bridge to CD
* Logs tell *what failed and why*
* Clean YAML is a DevOps skill

---

##  Session Outcome

✅ Participants can **author real build pipelines**
✅ Participants can **produce and consume artifacts**
✅ Participants are ready for **multi-stage pipelines**

---
