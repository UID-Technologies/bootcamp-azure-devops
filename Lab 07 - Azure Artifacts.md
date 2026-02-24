
## Module 4 Lab: Automation, Artifacts & Optimization 

### Scenario (what learners are building)

You maintain a repo containing:

1. A small **.NET 8 Web API** (build + test)
2. A **reusable pipeline template** for standard CI
3. A published **pipeline artifact** (drop)
4. A **universal package** published to **Azure Artifacts** (versioned)
5. Optimized pipelines using **caching**, **conditions**, **retries**, and **fail-fast** patterns

**Outcome:** A fast, reliable CI pipeline that publishes artifacts and a versioned package, ready to be consumed by your capstone multi-stage release pipeline.

---

# Prerequisites

### Tools

* Azure DevOps organization + project
* Agent:

  * Microsoft-hosted agent is fine (Windows or Ubuntu)
* Basic knowledge: YAML pipelines, repos, variables

### Permissions

* Ability to create pipelines
* Permission to create Azure Artifacts feed

---

# Part 0 — Create Sample Repo & Files (20 min)

## Step 0.1: Create a new repo

In Azure DevOps:

* **Repos → Files → New repository**
* Name: `module4-automation-artifacts`

## Step 0.2: Add a minimal .NET 8 Web API

Create folder:

* `src/Sample.Api`

Add a new Web API locally (or using dev machine) and push, OR add minimal files manually.
Suggested structure:

```
module4-automation-artifacts
├─ src/
│  └─ Sample.Api/
│     ├─ Sample.Api.csproj
│     ├─ Program.cs
├─ tests/
│  └─ Sample.Api.Tests/
│     ├─ Sample.Api.Tests.csproj
│     ├─ UnitTest1.cs
├─ build/
│  ├─ templates/
│  │  ├─ ci-dotnet.yml
│  ├─ scripts/
│  │  ├─ set-version.ps1
├─ azure-pipelines.yml
└─ README.md
```

### (Optional) Quick-create via dotnet CLI (recommended)

Commands (run locally then push):

```bash
dotnet new webapi -n Sample.Api -o src/Sample.Api
dotnet new xunit -n Sample.Api.Tests -o tests/Sample.Api.Tests
dotnet add tests/Sample.Api.Tests/Sample.Api.Tests.csproj reference src/Sample.Api/Sample.Api.csproj
dotnet sln new
dotnet sln add src/Sample.Api/Sample.Api.csproj
dotnet sln add tests/Sample.Api.Tests/Sample.Api.Tests.csproj
```

---

# Part 1 — Create Azure Artifacts Feed & Versioning Approach (25 min)

## Step 1.1: Create a Feed

Azure DevOps:

* **Artifacts → Create Feed**
* Feed name: `m4-feed`
* Visibility: Organization (or Project) based on your policy
* Upstream sources: optional (leave off for simplicity)

## Step 1.2: Decide versioning strategy (Simple + effective)

Use **SemVer**: `MAJOR.MINOR.PATCH`

* `MAJOR.MINOR` controlled by team
* `PATCH` auto-increment from pipeline or `Build.BuildId`

Example:

* Base version: `1.0`
* Generated: `1.0.$(Build.BuildId)`

This ensures every build publishes a unique version.

---

# Part 2 — Build Reusable Pipeline Template (Automation Pattern) (30 min)

## Goal

Create a template pipeline used by multiple pipelines later (capstone-ready).

## Step 2.1: Create template file `build/templates/ci-dotnet.yml`

This template:

* Restores
* Builds
* Tests
* Publishes build output as **pipeline artifact**
* Uses caching for NuGet
* Uses conditions and robust logging

**build/templates/ci-dotnet.yml**

```yaml
parameters:
  buildConfiguration: 'Release'
  dotnetVersion: '8.x'
  solution: '**/*.sln'
  testProjects: 'tests/**/*.csproj'
  publishProject: 'src/Sample.Api/Sample.Api.csproj'
  artifactName: 'drop'

steps:
- task: UseDotNet@2
  displayName: "Use .NET SDK ${{ parameters.dotnetVersion }}"
  inputs:
    packageType: 'sdk'
    version: '${{ parameters.dotnetVersion }}'

# Cache NuGet packages for speed
- task: Cache@2
  displayName: "Cache NuGet"
  inputs:
    key: 'nuget | "$(Agent.OS)" | **/*.csproj, **/packages.lock.json'
    restoreKeys: |
      nuget | "$(Agent.OS)"
    path: $(NUGET_PACKAGES)

- script: dotnet restore ${{ parameters.solution }}
  displayName: "Restore"

- script: dotnet build ${{ parameters.solution }} -c ${{ parameters.buildConfiguration }} --no-restore
  displayName: "Build"

- script: dotnet test ${{ parameters.testProjects }} -c ${{ parameters.buildConfiguration }} --no-build --logger trx
  displayName: "Test"
  continueOnError: false

- task: PublishTestResults@2
  displayName: "Publish Test Results"
  condition: succeededOrFailed()
  inputs:
    testResultsFormat: 'VSTest'
    testResultsFiles: '**/*.trx'
    failTaskOnFailedTests: true

- script: dotnet publish ${{ parameters.publishProject }} -c ${{ parameters.buildConfiguration }} -o $(Build.ArtifactStagingDirectory)/app
  displayName: "Publish App"

- task: PublishPipelineArtifact@1
  displayName: "Publish Pipeline Artifact (${{ parameters.artifactName }})"
  inputs:
    targetPath: '$(Build.ArtifactStagingDirectory)'
    artifact: '${{ parameters.artifactName }}'
```

✅ You now have a **standard CI template** that can be reused by Module 5.

---

# Part 3 — Main Pipeline YAML using Template + Variables (25 min)

## Step 3.1: Create `azure-pipelines.yml`

This pipeline:

* triggers on PR and main
* uses template
* sets variables
* demonstrates automation patterns (conditions + stages)

```yaml
trigger:
- main

pr:
- main

variables:
  buildConfiguration: 'Release'
  baseVersion: '1.0'
  # Unique, monotonic patch version
  packageVersion: '$(baseVersion).$(Build.BuildId)'

stages:
- stage: CI
  displayName: "CI - Build, Test, Publish Artifact"
  jobs:
  - job: BuildTest
    displayName: "Build & Test"
    pool:
      vmImage: 'windows-latest'
    steps:
    - checkout: self
      fetchDepth: 1

    - template: build/templates/ci-dotnet.yml
      parameters:
        buildConfiguration: $(buildConfiguration)
        artifactName: 'drop'
```

---

# Part 4 — Publish a Versioned Package to Azure Artifacts (Universal Packages) (30 min)

## Why Universal Packages?

Quick way to teach:

* feeds
* versioning
* publishing
* consuming later in capstone

## Step 4.1: Create package content folder

Add a folder:

* `package-content/`
  Inside add:
* `tooling-info.json`
* `README.txt`

Example `tooling-info.json`:

```json
{
  "name": "module4-tooling",
  "purpose": "Shared CI scripts/configs for capstone",
  "createdBy": "CI",
  "notes": "Published as a universal package"
}
```

## Step 4.2: Add a new job to publish universal package

Extend `azure-pipelines.yml` (add steps under the CI job **after template**):

```yaml
    - task: UniversalPackages@0
      displayName: "Publish Universal Package to Azure Artifacts"
      inputs:
        command: publish
        publishDirectory: 'package-content'
        feedsToUsePublish: 'internal'
        vstsFeedPublish: 'm4-feed'
        vstsFeedPackagePublish: 'module4-tooling'
        versionOption: 'custom'
        versionPublish: '$(packageVersion)'
        packagePublishDescription: 'Shared tooling/config artifacts for capstone'
```

✅ Now each build publishes:

* Pipeline artifact `drop`
* Universal package `module4-tooling` with version like `1.0.1234`

---

# Part 5 — Error Handling, Retries, and Resilience Patterns (25 min)

## Step 5.1: Add retry to a flaky step (pattern demo)

Example: wrap a step with retry logic using PowerShell (Windows agent). Add a new script step:

```yaml
    - powershell: |
        $max = 3
        for ($i=1; $i -le $max; $i++) {
          try {
            Write-Host "Attempt $i of $max"
            dotnet --info
            break
          } catch {
            if ($i -eq $max) { throw }
            Start-Sleep -Seconds (5 * $i)
          }
        }
      displayName: "Resilient Step Example (Retry Pattern)"
```

## Step 5.2: Always publish diagnostics even if build fails

Add:

```yaml
    - task: PublishPipelineArtifact@1
      displayName: "Publish Diagnostics"
      condition: always()
      inputs:
        targetPath: '$(Build.SourcesDirectory)'
        artifact: 'diagnostics'
```

(You can narrow this to logs only, but for teaching, it’s fine.)

---

# Part 6 — Pipeline Optimization Techniques (20 min)

Teach + implement these:

## Optimization 1: Shallow fetch

Already done: `fetchDepth: 1`

## Optimization 2: NuGet caching

Already implemented using `Cache@2`

## Optimization 3: Conditional execution (skip publish on PR)

Modify universal package publish step:

```yaml
    - task: UniversalPackages@0
      displayName: "Publish Universal Package to Azure Artifacts"
      condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
      inputs:
        command: publish
        publishDirectory: 'package-content'
        feedsToUsePublish: 'internal'
        vstsFeedPublish: 'm4-feed'
        vstsFeedPackagePublish: 'module4-tooling'
        versionOption: 'custom'
        versionPublish: '$(packageVersion)'
        packagePublishDescription: 'Shared tooling/config artifacts for capstone'
```

✅ PR builds still validate, but only `main` publishes packages.

---

# Demo Walkthrough (Instructor Script) (20–25 min)

Follow this sequence live:

1. **Introduce scenario**
   “We’re building a reusable CI template, publishing pipeline artifacts, and publishing versioned packages to Azure Artifacts.”

2. **Azure DevOps UI tour**

   * Pipelines → create pipeline → use existing YAML
   * Artifacts → show `m4-feed`
   * Pipelines → Runs → logs → artifacts

3. **Run the pipeline**

   * Trigger on main commit
   * Observe stages, caching, test results

4. **Inspect outputs**

   * Pipeline Artifacts tab: `drop`, `diagnostics`
   * Artifacts feed: `module4-tooling` with the generated version

5. **Connect to Module 5**

   * “In the capstone, the release pipeline will **consume** the published artifact/package, deploy across environments, and apply approvals/gates.”

---

# Common Pitfalls & Best Practices (10 min)

### Pitfalls

* Publishing packages from PR builds → pollutes feed
* Not versioning uniquely → overwrites / conflicts
* No caching → slow builds
* Not publishing test results when tests fail → hard troubleshooting
* Long pipelines with no templates → duplication everywhere

### Best Practices

* Use templates for repeatable jobs (CI standardization)
* Use `condition:` to control publish/deploy behavior
* Use `always()` for diagnostic artifacts
* Use caching for restore-heavy workflows
* Keep artifacts small and purposeful

---
