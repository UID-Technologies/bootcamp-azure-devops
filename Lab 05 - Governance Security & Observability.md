#  Lab 5: Governance, Security & Observability (Azure DevOps)

**Platform:** Azure DevOps
**Duration:** ~2 Hours
**Level:** Foundation → Intermediate

---

##  Lab Objectives

By the end of this lab, participants will be able to:

* Apply **pipeline-level security controls**
* Configure **service connections** securely
* Enforce **branch policies & PR validation**
* Protect pipelines using **permissions & approvals**
* Use **pipeline logs and diagnostics** for troubleshooting
* Understand **auditability and compliance** in Azure DevOps
* Prepare pipelines for **production governance**

---

##  Prerequisites

* Completed **Sessions 1–4 labs**
* Working **multi-stage pipeline** (Build → Dev → Test)
* Azure DevOps project: `ado-cicd-foundation`
* Basic familiarity with YAML pipelines and Git

---

##  Lab Scenario (Enterprise Context)

Your CI/CD pipelines are now business-critical.
You must ensure:

* Only authorized users can change pipelines
* Deployments are approved and auditable
* Secrets are protected
* Code quality gates are enforced
* Failures can be diagnosed quickly

----

![Image](https://learn.microsoft.com/en-us/azure/devops/organizations/security/media/about-security/security-groups-permission-management-cloud.png?view=azure-devops)

![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/media/azure-edit-service-connection.png?view=azure-devops)

![Image](https://learn.microsoft.com/en-us/azure/devops/repos/git/media/branch-policies/new-branches-page.png?view=azure-devops)

![Image](https://learn.microsoft.com/en-us/azure/devops/repos/git/media/branch-policies/policy-exempt-permission.png?view=azure-devops)

![Image](https://learn.microsoft.com/en-us/azure/devops/repos/git/media/branch-policies/branches-new-nav.png?view=azure-devops)

----


This lab simulates **production governance controls**.

---

#  PART 1: Azure DevOps Security Model (Conceptual)

## Step 1: Understand Azure DevOps Security Layers (Checkpoint)

Key security layers:

* Organization level
* Project level
* Repository level
* Pipeline level
* Environment level

💡 **Golden Rule:**
Security is **layered**, not centralized.

Trainer checkpoint ✔️

---

#  PART 2: Secure Repositories with Branch Policies

## Step 2: Create Feature Branch

1. Go to **Repos → Branches**
2. Create branch:

   ```
   feature/governance-lab
   ```
3. Make a small change (README or YAML)
4. Commit to feature branch

---

## Step 3: Configure Branch Policy for `main`

1. Go to **Repos → Branches**
2. Click **… → Branch policies** on `main`
3. Configure:

   * ✔ Require a minimum number of reviewers: **1**
   * ✔ Check for linked work items
   * ✔ Require successful build
4. Save

💡 **Learning Point:**
Branch policies prevent unreviewed code from reaching `main`.

---

## Step 4: Configure PR Validation Build

1. In branch policy → **Build validation**
2. Add build policy:

   * Pipeline: Your CI pipeline
   * Trigger: Automatic
3. Save

---

## Step 5: Raise Pull Request

1. Create PR from `feature/governance-lab` → `main`
2. Observe:

   * Build triggered automatically
   * PR blocked until checks pass
3. Approve PR
4. Complete merge

✅ Governance enforced successfully

---

#  PART 3: Secure Pipelines & Permissions

## Step 6: Review Pipeline Permissions

1. Go to **Pipelines → Your pipeline**
2. Click **… → Security**
3. Review permissions:

   * View pipeline
   * Edit pipeline
   * Queue builds

💡 Restrict **Edit pipeline** to DevOps engineers only.

---

## Step 7: Protect YAML Pipelines

Explain best practices:

* Protect `azure-pipelines.yml` via branch policy
* No direct commits to `main`
* Changes always via PR

(No config change required)

---

#  PART 4: Service Connections Security

## Step 8: Review Service Connections

1. Go to **Project Settings → Service connections**
2. Observe:

   * Azure Resource Manager connections (if any)
   * Permissions scope

💡 **Security Best Practice:**
Service connections should have **least privilege** and limited scope.

---

## Step 9: Restrict Pipeline Access to Service Connection

1. Open service connection
2. Go to **Pipeline permissions**
3. Set:

   * Grant access permission to **selected pipelines only**
4. Save

---

#  PART 5: Secure Secrets & Variables

## Step 10: Create Variable Group with Secret

1. Go to **Pipelines → Library**
2. Create **Variable Group**

   * Name: `secure-vars`
3. Add variable:

   * Name: `API_KEY`
   * Value: `dummy-secret`
   * ✔ Mark as secret
4. Save

---

## Step 11: Use Secret Variable in Pipeline

Reference in YAML:

```yaml
variables:
- group: secure-vars
```

Use in step:

```yaml
- script: |
    echo "Using secure variable"
  displayName: Secure variable usage
```

💡 Secret values never appear in logs.

---

#  PART 6: Environment-Level Governance

## Step 12: Review Environment Approvals

1. Go to **Pipelines → Environments**
2. Open `test` environment
3. Review:

   * Approvals
   * Deployment history

💡 Environment approvals are **release gates**, not CI checks.

---

## Step 13: Add Deployment Approval Timeout (Optional)

Configure:

* Timeout for approval
* Auto-reject after timeout

---

#  PART 7: Observability & Troubleshooting

## Step 14: Pipeline Logs Deep Dive

1. Open failed or successful pipeline run
2. Inspect:

   * Timeline view
   * Job-level logs
   * Step-level logs
3. Identify:

   * Failures
   * Warnings
   * Execution duration

---

## Step 15: Common Pipeline Failure Patterns (Discussion)

Discuss:

* YAML syntax errors
* Missing variables
* Permission issues
* Service connection failures
* Approval delays

---

#  PART 8: Auditability & Compliance

## Step 16: Review Audit Trail

Explain where audit data exists:

* Pipeline run history
* PR history
* Approval logs
* Environment deployment history

💡 Azure DevOps provides **built-in auditability**.

---

#  PART 9: Validation & Wrap-Up

## Step 17: Validation Checklist

✔ Branch policies enforced
✔ PR validation build configured
✔ Pipeline permissions reviewed
✔ Service connection restricted
✔ Secrets stored securely
✔ Environment approvals validated
✔ Pipeline logs analyzed

---

##  Key Takeaways

* Governance prevents bad deployments
* Branch policies enforce quality
* Pipelines must be permissioned
* Secrets must never be hard-coded
* Approvals provide release control
* Logs are your primary diagnostic tool
* Secure pipelines scale safely

---

##  Session Outcome

✅ Participants can **secure and govern Azure DevOps pipelines**
✅ Participants can **enforce quality gates and approvals**
✅ Participants are ready for **capstone CI/CD implementation**
