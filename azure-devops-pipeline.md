# Azure DevOps Pipeline Hierarchy (with Tasks & Scripts)

This document explains the structure of an Azure DevOps YAML pipeline
and how **tasks and scripts** both fit into the hierarchy.

**Hierarchy:**\
Pipeline → Stages → Jobs → Steps → (Tasks \| Scripts)

------------------------------------------------------------------------

## 📌 Overview

An Azure DevOps pipeline is composed of multiple levels:

-   **Pipeline**: The complete CI/CD workflow.
-   **Stage**: Major phases (e.g., Build, Test, Deploy).
-   **Job**: A set of steps executed on the same agent.
-   **Step**: An individual action in a job.
-   **Task**: A prebuilt reusable step provided by Azure DevOps.
-   **Script**: A custom command step (bash, pwsh, powershell, script).

> ✅ Both **tasks and scripts are types of steps**.

------------------------------------------------------------------------

## 🏗️ High-Level Hierarchy Diagram

``` mermaid
graph TD
    P[Pipeline]
    P --> S[Stages]
    S --> J[Jobs]
    J --> ST[Steps]
    ST --> T[Tasks]
    ST --> SC[Scripts]
```

------------------------------------------------------------------------

## 🧱 Detailed Tree Structure

``` mermaid
graph TD
    P[Pipeline]
    P --> S1[Stage: Build]
    P --> S2[Stage: Deploy]

    S1 --> J1[Job: BuildJob]
    J1 --> ST1[Step: Checkout]
    ST1 --> T1[Task: Checkout@1]

    J1 --> ST2[Step: Build]
    ST2 --> T2[Task: DotNetCoreCLI@2]

    J1 --> ST3[Step: Run Tests]
    ST3 --> SC1[Script: bash - run tests]

    J1 --> ST4[Step: Publish]
    ST4 --> T3[Task: PublishBuildArtifacts@1]

    S2 --> J2[Job: DeployJob]
    J2 --> ST5[Step: Pre-Deploy Check]
    ST5 --> SC2[Script: pwsh - validate]

    J2 --> ST6[Step: Deploy]
    ST6 --> T4[Task: Kubernetes@1]
```

------------------------------------------------------------------------

## 🧭 Execution Flow

Stages run sequentially by default.\
Jobs within a stage can run in parallel.\
Steps (tasks & scripts) inside a job always run sequentially.

``` mermaid
flowchart LR
    S1[Stage: Build] --> S2[Stage: Test] --> S3[Stage: Deploy]
```

------------------------------------------------------------------------

## 🧩 YAML Mapping with Tasks & Scripts

``` yaml
stages:
- stage: Build
  jobs:
  - job: BuildJob
    steps:
    - checkout: self

    - task: DotNetCoreCLI@2
      inputs:
        command: build

    - bash: |
        echo "Running unit tests"
        dotnet test
      displayName: Run Tests

    - script: echo "Build completed"

- stage: Deploy
  jobs:
  - job: DeployJob
    steps:
    - pwsh: |
        Write-Host "Validating before deploy"
      displayName: Pre-Deploy Validation

    - task: Kubernetes@1
      inputs:
        command: apply
```

------------------------------------------------------------------------

## 🔍 Component Explanation

### Pipeline

Top-level definition stored as a YAML file.

### Stage

Logical separation of work such as Build, Test, and Deploy.\
Stages can have approvals and environment checks.

### Job

Runs on a single agent or server.\
Types: - `job` (agent-based) - `deployment` (environment-based)

### Step

Single action in a job. Two main kinds: - **Task** → reusable plugin -
**Script** → custom shell/PowerShell commands

### Task

Reusable plugin maintained by Microsoft or partners.\
Examples: - `Docker@2` - `Kubernetes@1` - `AzureCLI@2`

### Script

Inline commands you control: - `bash` - `pwsh` - `powershell` - `script`

Used for glue logic, validations, or custom tooling.

------------------------------------------------------------------------

## 🔄 Parallelism View

``` mermaid
graph TD
    S[Stage: Build]
    S --> J1[Job: Linux Build]
    S --> J2[Job: Windows Build]
    S --> J3[Job: Security Scan]
```

------------------------------------------------------------------------

## ✅ Key Notes

-   A pipeline can have one or many stages.
-   Each stage must have at least one job.
-   Each job contains one or more steps.
-   **Steps can be either tasks or scripts.**
-   Scripts give flexibility; tasks give standardization.
-   Conditions and dependencies control execution flow.

------------------------------------------------------------------------

## 🚀 Conclusion

By combining **tasks for standard operations** and **scripts for custom
logic**, Azure DevOps pipelines become both powerful and flexible while
keeping a clear hierarchy.

------------------------------------------------------------------------
