#  Lab 3: Multi-Stage Pipelines & Environments (Azure DevOps)

**Platform:** Azure DevOps
**Duration:** ~2 Hours
**Level:** Foundation → Intermediate

---

##  Lab Objectives

By the end of this lab, participants will be able to:

* Convert a build-only pipeline into a **multi-stage YAML pipeline**
* Understand **stages, jobs, and dependencies**
* Use **Azure DevOps Environments**
* Configure **manual approvals**
* Promote **artifacts between stages**
* Observe and troubleshoot **stage-level execution**

---

## Prerequisites

* Completed **Session 1 & Session 2 labs**
* Working build pipeline that:

  * Builds successfully
  * Publishes artifacts
* Azure DevOps project: `ado-cicd-foundation`
* Basic understanding of YAML

---

##  Lab Scenario (Real-World Context)

Your team wants to:

* Automatically build code (CI)
* Deploy artifacts to **Dev**
* Require approval before deploying to **Test**
* Track deployments per environment

----
![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/architectures/media/azure-devops-ci-cd-architecture.svg?view=azure-devops)

![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/media/checks/environments.png?view=azure-devops)

![Image](https://stacksimplify.com/course-images/azure-devops-pipelines-key-concepts-1.png)



![Image](https://learn.microsoft.com/en-us/azure/batch/media/batch-ci-cd/deploymentflow.png)

----



This lab simulates a **real enterprise delivery pipeline**.

---

#  PART 1: Multi-Stage Pipeline Concepts

## Step 1: Understand Single vs Multi-Stage Pipelines (Checkpoint)

**Single-stage**

* CI only (build + test)

**Multi-stage**

* CI + CD
* Clear promotion path
* Environment-level controls

💡 **Mental Model**

```
Build → Dev → Test → Prod
```

Trainer checkpoint ✔️

---

#  PART 2: Create Azure DevOps Environments

## Step 2: Create Environments

1. Go to **Pipelines → Environments**
2. Click **Create environment**
3. Create:

   * **Name:** `dev`
   * **Type:** Virtual machine (generic)
4. Create another:

   * **Name:** `test`

✅ Environments created

---

## Step 3: Configure Approval on Test Environment

1. Open **test** environment
2. Go to **Approvals and checks**
3. Add **Approvals**
4. Configure:

   * Approver: Your user
   * Timeout: Default
5. Save

💡 **Learning Point:**
Approvals enforce **controlled releases**.

---

#  PART 3: Convert Pipeline to Multi-Stage YAML

## Step 4: Open Pipeline YAML

1. Go to **Pipelines**
2. Open your build pipeline
3. Click **Edit**

---

## Step 5: Convert YAML to Multi-Stage

Replace existing YAML with:

```yaml
trigger:
  branches:
    include:
      - main

variables:
  artifactName: 'drop'

stages:
- stage: Build
  displayName: Build Stage
  jobs:
  - job: BuildJob
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: NodeTool@0
      inputs:
        versionSpec: '18.x'
      displayName: Install Node.js

    - script: |
        npm install
        npm run build
      displayName: Build application

    - task: PublishBuildArtifacts@1
      inputs:
        PathtoPublish: '$(System.DefaultWorkingDirectory)'
        ArtifactName: '$(artifactName)'
      displayName: Publish artifact
```

💡 This is still CI — but now **explicitly staged**.

---

## Step 6: Add Dev Deployment Stage

Append to YAML:

```yaml
- stage: Dev
  displayName: Deploy to Dev
  dependsOn: Build
  jobs:
  - deployment: DeployDev
    environment: dev
    strategy:
      runOnce:
        deploy:
          steps:
          - download: current
            artifact: $(artifactName)

          - script: |
              echo "Deploying to DEV environment"
              ls -la $(Pipeline.Workspace)
            displayName: Dev deployment simulation
```

---

## Step 7: Add Test Deployment Stage (With Approval)

Append:

```yaml
- stage: Test
  displayName: Deploy to Test
  dependsOn: Dev
  jobs:
  - deployment: DeployTest
    environment: test
    strategy:
      runOnce:
        deploy:
          steps:
          - download: current
            artifact: $(artifactName)

          - script: |
              echo "Deploying to TEST environment"
              ls -la $(Pipeline.Workspace)
            displayName: Test deployment simulation
```

💡 Approval will be enforced automatically by the **test environment**.

---

#  PART 4: Run Multi-Stage Pipeline

## Step 8: Save & Run Pipeline

1. Click **Save**
2. Commit changes to `main`
3. Run pipeline

---

## Step 9: Observe Stage Execution

1. Pipeline executes **Build**
2. Automatically moves to **Dev**
3. Stops at **Test – Waiting for approval**

✅ Approval gate visible

---

## Step 10: Approve Test Deployment

1. Click **Review**
2. Approve deployment

Pipeline continues and completes **Test stage**.

---

#  PART 5: Inspect Artifacts & Deployments

## Step 11: Validate Artifact Promotion

1. Open **Dev stage logs**
2. Confirm artifact downloaded
3. Repeat for **Test stage**

💡 **Key Insight:**
Same artifact flows across environments — no rebuilds.

---

## Step 12: Review Environments Dashboard

1. Go to **Pipelines → Environments**
2. Open `dev` and `test`
3. Observe:

   * Deployment history
   * Status
   * Linked pipeline runs

---

#  PART 6: Failure & Control (Optional)

## Step 13: Simulate Failure in Test Stage

Modify Test script:

```bash
exit 1
```

Observe:

* Dev success
* Test failure
* Pipeline stops

💡 Controlled failure prevents bad releases.

---

#  PART 7: Validation & Wrap-Up

## Step 14: Validation Checklist

✔ Multi-stage YAML pipeline created
✔ Build stage produces artifact
✔ Dev stage deploys automatically
✔ Test stage requires approval
✔ Artifacts promoted correctly
✔ Environments show deployment history

---

##  Key Takeaways

* Multi-stage pipelines unify CI and CD
* Environments add governance and visibility
* Approvals enforce release discipline
* Artifacts must be immutable
* YAML stages reflect real delivery flow
* This pattern scales to **Prod**

---

##  Session Outcome

✅ Participants can design **end-to-end CI/CD pipelines**
✅ Participants understand **environment-based deployments**
✅ Participants are ready for **automation & optimization patterns**

