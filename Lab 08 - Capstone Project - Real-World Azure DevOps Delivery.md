# Lab 08: Capstone Project - Real-World Azure DevOps Delivery

**Platform:** Azure DevOps  
**Duration:** ~4 Hours  
**Level:** Foundation -> Intermediate  
**Persona:** DevOps Engineer / Platform Engineer / Technical Trainer

---

## Capstone Objective

Build a **realistic but manageable** end-to-end Azure DevOps implementation for a product team releasing a web API.

By the end of this lab, participants will:

* Implement **Git + PR governance** with branch policies
* Create a **YAML multi-stage pipeline** (CI + CD)
* Build, test, and publish **immutable artifacts**
* Use **Azure Artifacts feed** for package publishing
* Deploy across **Dev -> UAT** environments
* Configure **approval gates** before UAT
* Manage **secrets and variable groups** securely
* Validate **logs, audit trail, and troubleshooting flow**

---

## Business Scenario (Real-Time Context)

You are part of the DevOps enablement team at **Contoso Retail**.  
The Checkout API team must deliver frequent releases with controlled risk.

Leadership requirements:

* No direct commits to `main`
* Every change must pass PR checks
* Build once, deploy the same artifact across environments
* UAT deployment must require manual approval
* Secrets must not be exposed in logs
* Pipeline history must be auditable

This capstone simulates a **day-to-day enterprise delivery model**.

---

## What This Capstone Covers (Labs 01-07 Consolidation)

* Lab 01: CI/CD fundamentals, Azure DevOps navigation
* Lab 02: YAML authoring, variables, agents
* Lab 03: Multi-stage pipelines, environments, approvals
* Lab 04: Templates, optimization, conditions, caching
* Lab 05: Governance, security, observability
* Lab 06: End-to-end implementation
* Lab 07: Azure Artifacts feed and package lifecycle

---

## Prerequisites

* Azure DevOps organization access
* Permission to create project, repo, pipeline, environments
* Permission to create Artifacts feed
* A sample app repository (Node.js recommended)
* Optional: Azure subscription + service connection (if doing real deployment task)

---

## Target Architecture

```
Developer -> Feature Branch -> Pull Request -> main
                               |
                               v
                           CI Stage
                 (lint + test + build + artifact + package)
                               |
                               v
                         Deploy Dev (auto)
                               |
                               v
                      Deploy UAT (manual approval)
```

---

# PART 1: Project Bootstrap (30 min)

## Step 1: Create Project

1. Open `https://dev.azure.com`
2. Click **New Project**
3. Configure:
   * **Project name:** `ado-capstone-lab08`
   * **Visibility:** Private
   * **Version control:** Git
   * **Work item process:** Agile
4. Click **Create**

Validation:

* Project created successfully
* Left nav shows Repos, Pipelines, Artifacts, Boards

---

## Step 2: Create or Import Repository

Option A (Recommended for speed):

1. Go to **Repos -> Files**
2. Click **Import**
3. Use sample URL:
   ```
   https://github.com/microsoft/azure-devops-node-sample
   ```
4. Complete import

Option B (Bring your own app):

* Push a small Node.js API repo with:
  * `package.json`
  * test script
  * build script

Validation:

* Repo has application code and `package.json`
* Default branch noted (`main` or `master`)

---

## Step 3: Define Branching Convention

Use:

* `main` -> protected, stable
* `feature/*` -> development
* `hotfix/*` -> urgent production fix

No deployment should happen from feature branches.

---

# PART 2: Governance Baseline (35 min)

## Step 4: Configure `main` Branch Policies

1. Go to **Repos -> Branches**
2. On `main` (or default branch), click **... -> Branch policies**
3. Set:
   * Minimum reviewers: `1`
   * Check for linked work items: Enabled
   * Comment resolution required: Enabled
   * Build validation: Add your CI pipeline (after Step 8)

Validation:

* Direct low-quality merge to main is blocked

---

## Step 5: Pipeline and Repo Permissions Check

1. Go to **Pipelines -> [pipeline] -> ... -> Security** (after pipeline creation)
2. Confirm:
   * Only DevOps/Admin can edit pipeline
   * Contributors can queue builds if required
3. In repo security, ensure main branch is not open for direct force push

---

# PART 3: Build Reusable CI/CD Pipeline (75 min)

## Step 6: Create Template File

Create `templates/ci-steps.yml` in repo:

```yaml
parameters:
  nodeVersion: '18.x'

steps:
- task: NodeTool@0
  displayName: Use Node.js
  inputs:
    versionSpec: ${{ parameters.nodeVersion }}

- task: Cache@2
  displayName: Cache npm
  inputs:
    key: 'npm | "$(Agent.OS)" | package-lock.json'
    restoreKeys: |
      npm | "$(Agent.OS)"
    path: $(Pipeline.Workspace)/.npm

- script: npm ci
  displayName: Install dependencies
  env:
    npm_config_cache: $(Pipeline.Workspace)/.npm

- script: npm run lint --if-present
  displayName: Lint

- script: npm test -- --ci
  displayName: Run tests

- script: npm run build --if-present
  displayName: Build app
```

---

## Step 7: Create `azure-pipelines.yml`

Create or replace root `azure-pipelines.yml`:

```yaml
trigger:
  branches:
    include:
      - main

pr:
  branches:
    include:
      - main

variables:
  artifactName: 'drop'
  vmImage: 'ubuntu-latest'

stages:
- stage: CI
  displayName: CI Build and Validate
  jobs:
  - job: BuildAndTest
    pool:
      vmImage: $(vmImage)
    steps:
    - checkout: self
      fetchDepth: 1

    - template: templates/ci-steps.yml
      parameters:
        nodeVersion: '18.x'

    - task: PublishTestResults@2
      displayName: Publish test results
      condition: succeededOrFailed()
      inputs:
        testResultsFormat: 'JUnit'
        testResultsFiles: '**/test-results.xml'
        failTaskOnFailedTests: false

    - task: PublishPipelineArtifact@1
      displayName: Publish pipeline artifact
      inputs:
        targetPath: '$(Build.SourcesDirectory)'
        artifact: '$(artifactName)'

    - script: npm audit --audit-level=high || true
      displayName: Security audit (informational)

- stage: Deploy_Dev
  displayName: Deploy to Dev
  dependsOn: CI
  condition: succeeded()
  jobs:
  - deployment: DevDeploy
    environment: 'lab08-dev'
    strategy:
      runOnce:
        deploy:
          steps:
          - download: current
            artifact: $(artifactName)
          - script: |
              echo "Deploying artifact to DEV"
              ls -la $(Pipeline.Workspace)
            displayName: Dev deployment simulation

- stage: Deploy_UAT
  displayName: Deploy to UAT
  dependsOn: Deploy_Dev
  condition: succeeded()
  jobs:
  - deployment: UATDeploy
    environment: 'lab08-uat'
    strategy:
      runOnce:
        deploy:
          steps:
          - download: current
            artifact: $(artifactName)
          - script: |
              echo "Deploying artifact to UAT"
              ls -la $(Pipeline.Workspace)
            displayName: UAT deployment simulation
```

Trainer note:

* If your repo uses `master`, update `trigger` and `pr` branch filters accordingly.

---

## Step 8: Create Pipeline and Run

1. Go to **Pipelines -> Create Pipeline**
2. Select **Azure Repos Git**
3. Select your repo
4. Choose **Existing Azure Pipelines YAML file**
5. Select `/azure-pipelines.yml`
6. Save and run on `main`

Validation:

* CI stage executes
* Artifact is published
* Dev stage starts automatically
* UAT waits for approval (after Step 10)

---

# PART 4: Environments, Approvals, and CD Controls (40 min)

## Step 9: Create Environments

1. Go to **Pipelines -> Environments**
2. Create:
   * `lab08-dev`
   * `lab08-uat`

---

## Step 10: Add Approval for UAT

1. Open environment `lab08-uat`
2. Go to **Approvals and checks**
3. Add **Approvals**
4. Set at least one approver (trainer or team lead)
5. Save

Validation:

* Pipeline pauses before `Deploy_UAT`
* Approval action appears in run summary

---

# PART 5: Azure Artifacts Integration (30 min)

## Step 11: Create Feed

1. Go to **Artifacts**
2. Click **Create Feed**
3. Feed name: `lab08-feed`
4. Visibility: Project
5. Save

---

## Step 12: Publish a Universal Package from Pipeline

Add this task under CI stage after artifact publish:

```yaml
- task: UniversalPackages@0
  displayName: Publish shared package
  condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
  inputs:
    command: publish
    publishDirectory: '$(Build.SourcesDirectory)'
    feedsToUsePublish: 'internal'
    vstsFeedPublish: 'lab08-feed'
    vstsFeedPackagePublish: 'checkout-api-drop'
    versionOption: 'patch'
```

Validation:

* Package appears in `lab08-feed`
* Package versions increment on each main build

---

# PART 6: Secrets and Service Connections (25 min)

## Step 13: Create Variable Group with Secret

1. Go to **Pipelines -> Library -> Variable groups**
2. Create group: `lab08-secrets`
3. Add variables:
   * `APP_ENV=dev`
   * `API_TOKEN=<dummy-value>` (mark as secret)
4. Save

Link in YAML:

```yaml
variables:
- group: lab08-secrets
```

Use safely in script (do not echo secret value).

---

## Step 14: Service Connection Security (If Using Real Azure Deployments)

1. Go to **Project Settings -> Service connections**
2. Open your Azure Resource Manager service connection
3. Configure:
   * Least-privilege scope
   * Pipeline permissions to selected pipeline(s) only

If you are using deployment simulation only, still review these settings as a governance exercise.

---

# PART 7: Observability and Failure Drill (30 min)

## Step 15: Add a Controlled Failure Test

1. In feature branch, temporarily break a command in CI:
   * Example: change `npm test` command to invalid command
2. Raise PR to `main`
3. Observe:
   * PR validation fails
   * Merge blocked by policy
4. Fix command and rerun

Learning outcome:

* Team uses logs and policy gates to prevent bad merges.

---

## Step 16: Audit Trail Review

Validate and capture evidence:

* PR history with reviewer
* Build validation record
* Pipeline stage timeline
* UAT approval history
* Artifact/package version history

These are core compliance signals in enterprise delivery.

---

# PART 8: Final Validation and Submission (15 min)

## Step 17: Completion Checklist

✔ Project and repo ready  
✔ Branch policy enforced on main  
✔ PR validation active  
✔ Multi-stage YAML pipeline executed  
✔ Artifact published once and reused  
✔ Dev deployment auto-executed  
✔ UAT deployment approval enforced  
✔ Azure Artifacts package published  
✔ Secret variable group configured  
✔ Logs/audit evidence collected  

---

## Mandatory Deliverables

1. Final `azure-pipelines.yml`
2. `templates/ci-steps.yml`
3. Screenshot/evidence of:
   * Successful CI run
   * Dev deployment
   * UAT approval gate
   * Published package in Artifacts feed
4. Short `README` section explaining pipeline flow

---

## Evaluation Rubric (100%)

| Area | Weight |
|------|--------|
| CI pipeline quality (lint/test/build/artifacts) | 20% |
| CD design (Dev/UAT stages, dependencies) | 20% |
| Governance (branch policy + PR validation) | 20% |
| Security (secrets, permission hygiene) | 15% |
| Artifacts usage (pipeline + feed package) | 15% |
| Observability and troubleshooting evidence | 10% |

---

## Extension (Optional, Advanced)

If time permits, add:

* `Deploy_Prod` stage with stricter approvals
* Environment-specific variable templates
* Real deployment task to Azure App Service
* Basic rollback stage using previous successful artifact

---

## Capstone Outcome

After this lab, participants can implement a practical Azure DevOps delivery model that is:

* **governed** (PR + policy),
* **automated** (YAML CI/CD),
* **secure** (secrets + controlled access),
* **traceable** (logs + approvals + artifact history),
* and ready for real enterprise adoption.

