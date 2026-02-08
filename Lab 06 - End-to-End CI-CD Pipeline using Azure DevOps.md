
#  Capstone: End-to-End CI/CD Pipeline using Azure DevOps

**Platform:** Azure DevOps
**Duration:** **~3–4 Hours (Hands-on)**
**Level:** Novice → Foundation
**Persona:** DevOps / Platform Engineer

---

##  Capstone Objective

Design, build, secure, and operate a **production-style multi-stage CI/CD pipeline** that:

* Uses **YAML-based pipelines**
* Builds application code (CI)
* Publishes **immutable artifacts**
* Promotes artifacts across **Dev → Test**
* Enforces **approvals & governance**
* Uses **secure variables**
* Demonstrates **observability & auditability**

---

##  Mandatory Deliverables

1. Final multi-stage `azure-pipelines.yml`
2. Successful pipeline runs (Build, Dev, Test)
3. Artifact published and promoted
4. Approval evidence (Test stage)
5. README explaining pipeline flow
6. Screenshots/logs for validation

---

#  Capstone Scenario (Real-World)

Your organization is standardizing on **Azure DevOps CI/CD**.

You must deliver a pipeline that:

* Prevents unreviewed code from deploying
* Uses one build artifact across environments
* Requires approval before Test deployment
* Is secure, readable, and reusable
* Can be extended later to **Prod**

---

#  Architecture Overview

----
![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/architectures/media/azure-devops-ci-cd-architecture.svg?view=azure-devops)

![Image](https://phiptech.com/content/images/2023/05/Approval-Workflow.drawio--2-.png)

![Image](https://miro.medium.com/1%2AFfRDySrdcWsWDGi6w3YeNQ.jpeg)

![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/release/approvals/media/release-pipeline-workflow.svg?view=azure-devops)

![Image](https://media.licdn.com/dms/image/v2/C4D12AQEN6siN6A0BVg/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1586785287223?e=2147483647\&t=Lrs9tc0nDuzrr20cxgW1YLh1vBH5Rz1Kn3Qgv1JSu-g\&v=beta)
----

```
Git Repo
  ↓
Build Stage (CI)
  ↓  (Artifact)
Dev Stage (Auto Deploy)
  ↓
Test Stage (Manual Approval)
```

**Core Components**

* Azure Repos (Git)
* YAML Pipeline
* Azure Artifacts (Pipeline artifacts)
* Azure DevOps Environments
* Variable Groups (Secrets)
* Branch Policies

---

#  PHASE 1: Planning & Governance (30 Minutes)

## Step 1: Confirm Repository Structure

Your repo should contain:

```
/
├── src/
├── package.json / build files
├── templates/
│   └── build-steps.yml
├── azure-pipelines.yml
└── README.md
```

---

## Step 2: Governance Preconditions

Ensure:

* `main` branch protected
* PR validation build enabled
* DevOps pipeline permissions restricted
* Test environment approval configured

(From Session 5 lab)

---

#  PHASE 2: Build the Capstone Pipeline (90 Minutes)

## Step 3: Create / Replace `azure-pipelines.yml`

```yaml
trigger:
  branches:
    include:
      - main

variables:
  artifactName: 'drop'

stages:
# =====================
# BUILD STAGE (CI)
# =====================
- stage: Build
  displayName: Build Stage
  jobs:
  - job: BuildJob
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - template: templates/build-steps.yml

    - task: PublishBuildArtifacts@1
      displayName: Publish Build Artifact
      inputs:
        PathtoPublish: '$(System.DefaultWorkingDirectory)'
        ArtifactName: '$(artifactName)'

# =====================
# DEV STAGE (AUTO)
# =====================
- stage: Dev
  displayName: Deploy to Dev
  dependsOn: Build
  condition: succeeded()
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
              echo "Deploying to DEV"
              ls -la $(Pipeline.Workspace)
            displayName: Dev deployment simulation

# =====================
# TEST STAGE (APPROVAL)
# =====================
- stage: Test
  displayName: Deploy to Test
  dependsOn: Dev
  condition: succeeded()
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
              echo "Deploying to TEST"
              ls -la $(Pipeline.Workspace)
            displayName: Test deployment simulation
```

---

## Step 4: Commit & Push Changes

Commit to `main` using PR flow:

```bash
git add .
git commit -m "Capstone: multi-stage CI/CD pipeline"
git push origin feature/capstone
```

Raise PR → validate → approve → merge.

---

#  PHASE 3: Execute & Validate Pipeline (45 Minutes)

## Step 5: Run Pipeline

1. Go to **Pipelines**
2. Select pipeline → **Run**
3. Observe stage execution

---

## Step 6: Validate Build Stage

Confirm:

* Build steps executed
* Artifact `drop` created
* Logs are clean

---

## Step 7: Validate Dev Deployment

Confirm:

* Dev stage auto-deploys
* Artifact downloaded
* Environment shows deployment history

---

## Step 8: Approve Test Deployment

1. Pipeline pauses at **Test**
2. Click **Review**
3. Approve deployment

Confirm Test stage completes successfully.

---

#  PHASE 4: Security & Observability Validation (30 Minutes)

## Step 9: Secret Validation

Confirm:

* Secrets stored in Variable Group
* No secrets visible in logs
* Pipeline runs without exposing values

---

## Step 10: Pipeline Logs & Audit Trail

Inspect:

* Stage-level logs
* Approval history
* Deployment timeline
* Commit → pipeline linkage

💡 This satisfies **compliance & audit requirements**.

---

#  PHASE 5: Documentation & Handover (30 Minutes)

## Step 11: Update README.md

Include:

* Pipeline overview
* Stage descriptions
* Approval flow
* How to trigger pipeline
* How to extend to Prod

---

## Step 12: Final Validation Checklist

✔ YAML pipeline committed
✔ Multi-stage pipeline executed
✔ Artifact built once and reused
✔ Dev auto deployment successful
✔ Test deployment approval enforced
✔ Secrets protected
✔ Logs and history auditable

---

#  Evaluation Rubric (100%)

| Area                   | Weight |
| ---------------------- | ------ |
| Pipeline Design & Flow | 25%    |
| YAML Quality & Reuse   | 20%    |
| Artifact Management    | 15%    |
| Governance & Security  | 20%    |
| Observability & Logs   | 10%    |
| Documentation          | 10%    |

---

##  Capstone Outcomes

After completing this capstone, participants can:

* Design **real CI/CD pipelines**
* Implement **multi-stage YAML pipelines**
* Use **artifacts & environments correctly**
* Apply **governance and approvals**
* Secure pipelines and secrets
* Operate pipelines like a **production DevOps engineer**

---

##  Course Completion Statement

✅ Participant is **job-ready at Foundation level** for:

* Azure DevOps CI/CD
* YAML pipelines
* DevOps / Platform Engineer roles

