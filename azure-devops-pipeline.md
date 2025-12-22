# Azure DevOps Pipeline Hierarchy

This document explains the structure of an Azure DevOps YAML pipeline
and how different components relate to each other.

**Hierarchy:**\
Pipeline → Stages → Jobs → Steps → Tasks

------------------------------------------------------------------------

## 📌 Overview

An Azure DevOps pipeline is composed of multiple levels:

-   **Pipeline**: The complete CI/CD workflow.
-   **Stage**: Major phases (e.g., Build, Test, Deploy).
-   **Job**: A set of steps executed on the same agent.
-   **Step**: An individual action (script or task).
-   **Task**: A prebuilt reusable step provided by Azure DevOps.

------------------------------------------------------------------------

## 🏗️ High-Level Hierarchy Diagram

``` mermaid
graph TD
    P[Pipeline]
    P --> S[Stages]
    S --> J[Jobs]
    J --> ST[Steps]
    ST --> T[Tasks]
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
    J1 --> ST3[Step: Publish]
    ST3 --> T3[Task: PublishBuildArtifacts@1]

    S2 --> J2[Job: DeployJob]
    J2 --> ST4[Step: Deploy]
    ST4 --> T4[Task: Kubernetes@1]
```

------------------------------------------------------------------------

## 🧭 Execution Flow

Stages run sequentially by default.\
Jobs within a stage can run in parallel.\
Steps inside a job always run sequentially.

``` mermaid
flowchart LR
    S1[Stage: Build] --> S2[Stage: Test] --> S3[Stage: Deploy]
```

------------------------------------------------------------------------

## 🧩 YAML Mapping to Hierarchy

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
    - script: echo "Build completed"

- stage: Deploy
  jobs:
  - job: DeployJob
    steps:
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

Single action in a job: - Script (`bash`, `pwsh`, `script`) - Task
invocation

### Task

Reusable plugin maintained by Microsoft or partners.\
Examples: - `Docker@2` - `Kubernetes@1` - `AzureCLI@2`

> Note: Tasks are a specialized type of step.

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
-   Tasks and scripts are both steps.
-   Conditions and dependencies control execution flow.

------------------------------------------------------------------------

## 🚀 Conclusion

Understanding this hierarchy helps in designing modular, scalable, and
maintainable CI/CD pipelines using Azure DevOps.

------------------------------------------------------------------------
