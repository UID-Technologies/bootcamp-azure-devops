#  Lab 4: Automation, Artifacts & Optimization (Azure DevOps)

**Platform:** Azure DevOps
**Duration:** ~2 Hours
**Level:** Foundation → Intermediate

---

##  Lab Objectives

By the end of this lab, participants will be able to:

* Use **Azure Artifacts** for versioned packages/artifacts
* Create **reusable YAML templates**
* Implement **conditional execution**
* Add **error handling and retries**
* Apply **pipeline caching** for faster builds
* Optimize pipeline structure and execution time

---

##  Prerequisites

* Completed **Sessions 1–3 labs**
* Working **multi-stage pipeline** with Build → Dev → Test
* Azure DevOps project: `ado-cicd-foundation`
* Build artifacts being published successfully

---

##  Lab Scenario (Enterprise Context)

Your CI/CD pipeline works, but:

* YAML is getting duplicated
* Builds are slow
* Failures are not handled cleanly
* Artifacts need proper versioning and reuse

-----

![Image](https://learn.microsoft.com/de-de/azure/devops/artifacts/concepts/media/upstream-source-graph-4.svg?view=azure-devops)

![Image](https://damienaicheh.github.io/assets/posts/2022-03-27-folder-architecture.png)

![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/media/run-overview.png?view=azure-devops)

![Image](https://docs.port.io/img/guides/azureDevOpsDeploymentDashboard1.png)

![Image](https://i.sstatic.net/z2jo6.png)

----


Your task is to **industrialize** the pipeline.

---

#  PART 1: Azure Artifacts – Foundations

## Step 1: Understand Azure Artifacts (Checkpoint)

Azure Artifacts provides:

* Centralized artifact feeds
* Versioning & retention
* Secure consumption across pipelines

Common uses:

* NPM packages
* NuGet packages
* Generic artifacts

Trainer checkpoint ✔️

---

## Step 2: Create an Azure Artifacts Feed

1. Go to **Artifacts**
2. Click **Create Feed**
3. Configure:

   * **Name:** `foundation-feed`
   * **Scope:** Project
   * **Upstream sources:** Disabled (for lab)
4. Click **Create**

✅ Artifact feed created

---

## Step 3: Connect Pipeline to Artifacts Feed (Read-Only)

1. Open feed → **Connect to feed**
2. Review instructions for:

   * npm
   * NuGet
   * Maven

💡 **Learning Point:**
Feeds act as **trusted internal package sources**.

---

#  PART 2: YAML Templates – Reusability

## Step 4: Identify Repeated YAML

In your pipeline, note repeated patterns:

* Agent pool definition
* Node.js install
* Build steps

💡 These should be **templated**.

---

## Step 5: Create a Pipeline Template

1. Go to **Repos**
2. Create folder:

   ```
   /templates
   ```
3. Create file:

   ```
   templates/build-steps.yml
   ```

Paste:

```yaml
parameters:
  nodeVersion: '18.x'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: ${{ parameters.nodeVersion }}
  displayName: Install Node.js

- script: |
    npm install
    npm run build
  displayName: Build application
```

Commit changes.

---

## Step 6: Use Template in Pipeline

Modify **Build stage**:

```yaml
steps:
- template: templates/build-steps.yml
  parameters:
    nodeVersion: '18.x'
```

✅ Pipeline now uses reusable logic

---

# PART 3: Conditional Execution

## Step 7: Add Branch-Based Conditions

Modify **Dev stage**:

```yaml
condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
```

💡 Deploy only from `main`.

---

## Step 8: Add Environment-Based Conditions

Example:

```yaml
condition: and(succeeded(), ne(variables['Build.Reason'], 'PullRequest'))
```

💡 Avoid deployments during PR validation.

---

#  PART 4: Error Handling & Retries

## Step 9: Add Retry Logic to Tasks

Example:

```yaml
- script: npm install
  retryCountOnTaskFailure: 2
  displayName: Install dependencies with retry
```

💡 Useful for flaky network operations.

---

## Step 10: Control Failure Behavior

Example:

```yaml
- script: echo "Optional step"
  continueOnError: true
```

💡 Use sparingly—never hide critical failures.

---

# PART 5: Pipeline Caching (Performance Optimization)

## Step 11: Add Cache Task

Add before `npm install`:

```yaml
- task: Cache@2
  inputs:
    key: 'npm | "$(Agent.OS)" | package-lock.json'
    restoreKeys: |
      npm | "$(Agent.OS)"
    path: ~/.npm
  displayName: Cache npm dependencies
```

---

## Step 12: Run Pipeline & Observe Speed Improvement

1. Run pipeline twice
2. Compare:

   * First run: cache miss
   * Second run: cache hit

💡 **Learning Point:**
Caching dramatically improves CI performance.

---

#  PART 6: Artifact Versioning Strategy (Conceptual)

Discuss:

* Semantic versioning (MAJOR.MINOR.PATCH)
* Build ID–based versions
* Immutable artifacts

Example:

```text
app-1.0.$(Build.BuildId)
```

---

# PART 7: Validate Optimized Pipeline

## Step 13: Full Pipeline Run

Run pipeline and verify:

* Template usage
* Conditional execution
* Cache hits
* Artifacts published
* Stages behave correctly

---

## Step 14: Review YAML Readability

Confirm:

* No duplicated logic
* Clear stage/job naming
* Reusable templates
* Minimal complexity

---

#  PART 8: Validation & Wrap-Up

## Step 15: Validation Checklist

✔ Azure Artifacts feed created
✔ YAML templates implemented
✔ Pipeline refactored for reuse
✔ Conditional execution applied
✔ Retry & error handling added
✔ Pipeline caching enabled
✔ Pipeline execution optimized

---

##  Key Takeaways

* Reusability reduces maintenance cost
* Templates are critical for scale
* Conditions prevent accidental deployments
* Retries improve resilience
* Caching saves time and money
* Optimized pipelines are **production pipelines**

---

## 🎓 Session Outcome

✅ Participants can **industrialize CI/CD pipelines**
✅ Participants understand **artifacts and automation patterns**
✅ Participants can optimize pipelines for **speed and reliability**

