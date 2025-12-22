# Azure DevOps Release Pipeline Hierarchy (Classic Releases)

This document describes the structure of **Azure DevOps Classic Release
pipelines** and how artifacts flow through environments to deploy your
application.

**Hierarchy:**\
Release → Stages (Environments) → Agent Phases → Tasks

------------------------------------------------------------------------

## 📌 Overview

A Release pipeline is used for Continuous Delivery (CD). It takes build
artifacts and deploys them across environments like Dev, QA, and Prod
with approvals and gates.

Key components:

-   **Release**: A single deployment instance created from artifacts.
-   **Stage / Environment**: Logical deployment targets (Dev, Test,
    Prod).
-   **Agent Phase (Job)**: Group of tasks run on the same agent.
-   **Task**: Individual deployment or automation action.
-   **Artifacts**: Inputs produced by CI pipelines.

------------------------------------------------------------------------

## 🏗️ High-Level Hierarchy

``` mermaid
graph TD
    R[Release Pipeline]
    R --> S[Stages / Environments]
    S --> P[Agent Phases]
    P --> T[Tasks]
```

------------------------------------------------------------------------

## 🧱 Detailed Example Structure

``` mermaid
graph TD
    R[Release]

    R --> Dev[Stage: Dev]
    R --> QA[Stage: QA]
    R --> Prod[Stage: Prod]

    Dev --> DevPhase[Agent Phase]
    DevPhase --> DevTask1[Task: Extract Files]
    DevPhase --> DevTask2[Task: Deploy to App Service]

    QA --> QAPhase[Agent Phase]
    QAPhase --> QATask1[Task: Run Tests]
    QAPhase --> QATask2[Task: Deploy to AKS]

    Prod --> ProdPhase[Agent Phase]
    ProdPhase --> ProdTask1[Task: Manual Validation]
    ProdPhase --> ProdTask2[Task: Deploy to Prod]
```

------------------------------------------------------------------------

## 🧭 Promotion Flow Across Environments

``` mermaid
flowchart LR
    A[Artifact] --> D[Dev]
    D --> Q[QA]
    Q --> P[Prod]
```

Each stage can have: - Pre-deployment approvals - Post-deployment
approvals - Gates (query-based checks)

------------------------------------------------------------------------

## 📦 Artifacts Flow

``` mermaid
graph LR
    B[Build Pipeline] --> A[Artifact]
    A --> R[Release Pipeline]
    R --> Dev[Dev]
    Dev --> QA[QA]
    QA --> Prod[Prod]
```

------------------------------------------------------------------------

## 🧩 Typical Tasks in Releases

Common built-in tasks: - Azure App Service Deploy - Kubernetes Deploy -
IIS Web App Deploy - PowerShell / Bash - Azure CLI - Manual Validation

------------------------------------------------------------------------

## 🔍 Component Explanation

### Release

A release is created when a new artifact version is available or
manually triggered.\
Each release runs through all configured stages.

### Stage / Environment

Represents a deployment target like: - Dev - QA / UAT - Staging -
Production

Stages support: - Approvals - Gates - Variables - Triggers

### Agent Phase (Job)

A logical group of tasks that: - Runs on the same agent - Can be
agent-based or server-based - Executes tasks sequentially

### Task

Smallest unit of work, such as: - Deploying code - Running scripts -
Executing tests

Tasks are executed inside an agent phase.

### Artifacts

Inputs to the release pipeline, typically from: - Azure Pipelines
(Build) - GitHub - Jenkins - Azure Container Registry (images)

------------------------------------------------------------------------

## 🔄 Parallel Stages Example

``` mermaid
graph TD
    R[Release]
    R --> Dev1[Dev-East]
    R --> Dev2[Dev-West]
```

Multiple environments can deploy in parallel if configured.

------------------------------------------------------------------------

## 🆚 Classic Releases vs YAML CD

  Classic Release Pipeline   YAML Multi-Stage Pipeline
  -------------------------- ---------------------------
  Release UI-based           YAML in repo
  Stages = Environments      stages: in YAML
  Agent Phases               jobs:
  Tasks in UI                steps/tasks in YAML
  Approvals per stage        environments + checks

------------------------------------------------------------------------

## 🚀 Conclusion

Azure DevOps Release pipelines provide a powerful UI-driven CD model
with: - Artifact promotion - Environment-based approvals - Controlled
deployments to production

They are ideal for teams using classic pipelines or transitioning to
YAML-based CD.

------------------------------------------------------------------------
