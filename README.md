# 🚀 rooroo (如如): Minimalist AI Orchestration with Specialist Agents 🚀

**Version: v0.2.0** [Changelog](changelog.md)

> **Note on v0.2.0 (Breaking Change & Optimization):** This version introduces major architectural shifts focused on efficiency and cost optimization. Key changes include:
> * **Coordinator-Led Workflow & Triage:** `Workflow Coordinator` (Cheap Model) is now the primary interface, performing rule-based triage and invoking the `Strategic Planner` (Smart Model) on-demand.
> * **Decoupled State & Signal-Driven Execution:** Agents (`coder-monk`, specialists) now create their own task state file (`.state/tasks/{taskId}.json`) upon completion. The Coordinator waits for completion signals, reads these files, validates them, and performs batch updates to `project_overview.json`.
> * **Planner-Guided Delegation:** The Planner defines tasks in `project_overview.json` with a `suggested_mode` (e.g., `coder-monk`, `solution-architect`), which the Coordinator strictly follows for delegation.
> * **`coder-monk`:** Introduced a dedicated `coder-monk` for coding/debugging tasks, replacing direct reliance on built-in modes in the core workflow.
> * **Cost Optimization Focus:** Explicit model tier recommendations (Smart/Cheap) and minimized API calls via decoupled state and batch overview updates.
> **[See v0.2.0 rationale for details.](v0.2.0.md)**

> **Note on v0.1.0 (Previous Breaking Change):** v0.1.0 introduced the Planner/Coordinator split and removed the `Apex Implementer` in favor of built-in IDE coding modes. [See v0.1.0 rationale.](v0.1.0.md)

Welcome to `rooroo`, an AI-powered system designed to achieve **minimalist AI orchestration** for software development using a focused crew of **specialist agents** right within your VS Code environment via the [Roo Code extension](https://github.com/RooVetGit/Roo-Code). Think of it as having a lean, expert virtual team, precisely coordinated through distinct planning and execution phases, now optimized for efficiency, cost, and clarity via a **Coordinator-led, signal-driven workflow**.

## 🤔 What's in a Name? The Meaning of "rooroo (如如)"

The name "rooroo" comes from the term **"如如" (rú rú)** found in Buddhist philosophy. It relates to the concept of **Tathātā**, often translated as "Thusness" or "Suchness."

![img](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQeUmsB4LIHLErFEbei5g8PfIFG-XQntgqLyA&s)

* **Thusness/Suchness (真如):** Refers to the fundamental, unchanging, true nature of all phenomena – reality as it is.
* **Equality & Non-Duality (不二平等):** Implies that, at the ultimate level, all things share this same essential nature.
* **Emphasis:** The repetition "如如" emphasizes that this inherent "thusness" is pervasive across *all* things.

In the context of this project, the name evokes the idea of an underlying, consistent nature guiding the orchestration. This philosophy informs the **minimalist approach**, focusing on the essential 'thusness' of each agent's specialized role.

## 🎯 Key Problems Addressed

1.  **Over-Complexity & Lack of Focus:** Addressed with a **minimalist crew of highly specialized agents** with distinct Smart/Cheap model tiers.
2.  **Interaction Overhead & Lack of Clear Process:** Solved with an **efficient, Coordinator-led, signal-driven orchestration model:**
    * **🚦 Workflow Coordinator (Primary Interface / Cheap Model Recommended):** Acts as the primary interaction point. Performs rule-based triage on initial requests. Manages plan execution mechanically by following Planner's `suggested_mode`, **waiting for completion signals**, **reading agent-specific state files** (`.state/tasks/{taskId}.json`), validating them, and updating `project_overview.json` via **batch operations**.
    * **🏛️ Strategic Planner (On-Demand / Smart Model Recommended):** Handles focused planning tasks when invoked by the Coordinator. Defines tasks, `delegation_details`, and crucially, the `suggested_mode` in `project_overview.json`.
    * **🧘‍♂️ Coder Monk (Custom):** Handles specific coding/refactoring/debugging tasks, creating its own state file (`.state/tasks/{taskId}.json`) on completion.
3.  **Inconsistent Development Practices:** Promotes **Document-Driven Development (DDD) and Test-Driven Development (TDD)** principles through its structured workflow, agent roles (Planner, Architect, Validator), and artifacts.
4.  **Cost Inefficiency:** Addressed through targeted use of **Smart vs. Cheap LLMs** per role and **optimizing API calls** (decoupled state files created by agents, batch overview updates by Coordinator).

## 🤔 Design Philosophy

The core principles are refined for efficiency and clarity:

* **Simplicity & Minimalism:** Keep the agent team focused.
* **Specialized Roles (Smart/Cheap):** Each agent has a clear role and designated model tier.
* **Coordinator-Led, Signal-Driven Orchestration:** Coordinator acts as primary interface, performs triage, drives process based on Planner guidance and completion signals.
* **Planner-Guided Execution:** Coordinator relies strictly on Planner's `suggested_mode` for delegation.
* **Decoupled State Reporting:** Executing agents report results by creating their *own* state file; Coordinator reads these files to update the central overview.
* **Dedicated Coder Monk:** Uses a specific `coder-monk` for implementation tasks.
* **Document & Test-Based Approach:** Emphasize clarity and reliability through DDD/TDD.
* **Cost-Effectiveness:** Optimize LLM usage (Smart/Cheap roles) and minimize API interactions (decoupled state, batch overview updates).

## ✨ Why Use rooroo v0.2.0? ✨

`rooroo` offers a structured, efficient, and potentially more cost-effective approach:

* **🎯 Focused Expertise:** Delegate complex planning and design to **Smart** specialists, leverage **Cheap** specialists for validation/docs, and use a dedicated **Coder Monk** for implementation. (Solves Problem 1 & 4)
* **⚙️ Efficient Orchestration:** Interact primarily with the **Cheap** `Workflow Coordinator`, which performs triage, manages the flow via signals and Planner guidance, reads agent state files, and uses batch overview updates. (Solves Problem 2 & 4)
* **⚡ Streamlined Implementation:** Uses a dedicated **`coder-monk`** as directed by the Planner's `suggested_mode`. (Solves Problem 2 & 3)
* **🏗️ Structured Workflow:** Follows a defined process (Triage, Plan, Delegate, Execute & Report State, Process Results, Update Overview), encouraging DDD/TDD. (Solves Problem 3)
* **💾 Clear Artifacts & State:** Organizes outputs (`.state/specs/`, `.state/design/`, etc.) and provides both a central overview (`project_overview.json`) and detailed task states (`.state/tasks/{taskId}.json`) created by the executing agent. (Supports Problem 3)
* **💰 Cost Optimized:** Designed for cost savings through differentiated **Smart/Cheap LLM** use, decoupled state reporting, and batch overview updates. (Solves Problem 4)

## 🔑 Core Concepts

1.  **Minimalist Agent Crew:** Planner (Smart), Coordinator (Cheap), Architect (Smart), UX Specialist (Smart), Validator (Cheap), DocuCrafter (Cheap), Coder Monk (Custom).
2.  **Coordinator-Led, Signal-Driven Orchestration with Planner Guidance:**
    * **🚦 Workflow Coordinator (Primary / Cheap):** Performs rule-based triage. Invokes Planner if needed. Manages execution by following Planner's `suggested_mode`. **Waits for completion signals**, **reads results from agent-specific task files** (`.state/tasks/{taskId}.json`), validates them, and updates overview state via **batch edits**.
    * **🏛️ Strategic Planner (On-Demand / Smart):** Executes planning tasks when called by Coordinator. Populates `project_overview.json` (with `delegation_details` including `suggested_mode` and `NNN:type:subject` `taskId`). **Does not create `.state/tasks/` files.**
3.  **Specialist Roles (Smart/Cheap/Custom):** Architect (Smart), UX Specialist (Smart), Validator (Cheap), DocuCrafter (Cheap), Coder Monk (Custom) handle specific tasks suggested by Planner.
4.  **Planner-Driven Delegation:** Coordinator uses `suggested_mode` from the overview for delegation.
5.  **Decoupled State Creation & Reporting:** Executing agents (Specialists, Coder) **create and write ONLY their own task file** (`.state/tasks/{taskId}.json`) upon completion/failure. Coordinator reads these files.
6.  **Structured Artifacts:** Relies on outputs organized within `.state/` subdirectories: `.state/specs/`, `.state/design/`, `.state/docs/`, `.state/reports/`.

## 📁 Directory Structure

This diagram shows the typical directory layout generated and managed by `rooroo`:

```
<Project Root>/
├── .state/                   # Internal state managed by workflow
│   └── tasks/                # Individual task state files (CREATED by the agent executing the task)
│       ├── 010:chore:setup_project.json # Example: Created by Coder Monk on completion
│       ├── 020:design:api_spec.json    # Example: Created by Solution Architect on completion
│       ├── 030:feat:implement_login.json # Example: Created by Coder Monk on completion
│       ├── 040:docs:readme_update.json   # Example: Created by DocuCrafter on completion
│       └── 050:test:validate_login.json  # Example: Created by Guardian Validator on completion
│   │
│   └── specs/                # Output from Solution Architect (Smart)
│       └── 020:design:api_spec/
│           └── user_service_api.md
│           └── database_schema.md
│   │
│   └── design/               # Output from UX Specialist (Smart)
│       └── NNN:design:some_ux/
│           └── style_guide.md
│   │
│   └── docs/                 # Output from DocuCrafter (Cheap)
│       └── 040:docs:readme_update/
│           └── updated_readme_section.md
│   │
│   └── reports/              # Output from Guardian Validator (Cheap)
│       └── 050:test:validate_login_report.md
│
├── project_overview.json     # Central plan & task list (Managed ONLY by Coordinator & Planner)
│
└── src/                      # Example source code directory
    └── main.js
    └── api.js
    # ... other source files modified by Coder Monk
```

## 💡 LLM Cost Optimization

`rooroo`'s architecture allows using different LLM tiers for different roles, potentially saving significant costs.

| Agent Role             | Recommended Model Tier | Rationale                                                                        | Example Models (Illustrative)          |
| :--------------------- | :--------------------- | :------------------------------------------------------------------------------- | :------------------------------------- |
| **Workflow Coordinator** | **⚡ Cheap/Fast**     | Primary interface, rule-based triage, signal-driven execution, reads state, batch updates. | Gemini Flash, Claude Haiku, Grok-1 | 
| **Strategic Planner** | **🧠 Smart/Expensive** | Complex goal interpretation, decomposition, planning, suggesting modes.          | GPT-4o, Claude 3 Sonnet/Opus, Gemini 1.5/2 Pro |
| **Solution Architect** | **🧠 Smart/Expensive** | Deep technical design, defining sub-tasks, complex specifications.               | GPT-4o, Claude 3 Sonnet/Opus, Gemini 1.5/2 Pro |
| **UX Specialist** | **🧠 Smart/Expensive** | UI structure, UX flows, potentially complex design assets.                       | GPT-4o, Claude 3 Sonnet/Opus, Gemini 1.5/2 Pro |
| **Guardian Validator** | **⚡ Cheap/Fast**     | Executing defined tests/checks, structured reporting via state file & reports. | Gemini Flash, Claude Haiku, Grok-1 | 
| **DocuCrafter** | **⚡ Cheap/Fast**     | Generating/updating documentation based on context, reporting via state file. | Gemini Flash, Claude Haiku, Grok-1 | 
| **Coder Monk** | *Custom / Varies* | Executes coding/debugging, creates state file. Model depends on complexity. | GPT-4o, Claude 3 Sonnet, Gemini 1.5/2 Pro |

Configure the underlying LLM for each agent mode (if supported by your environment) to optimize cost vs. capability.

## 🔄 The Core Development Workflow

1.  **🎯 Goal Setting & Triage:** You provide your goal to the **🚦 Workflow Coordinator (Cheap)**. It performs rule-based triage:
    *   *Simple Code/Debug:* Delegates directly to **🧘‍♂️ Coder Monk**.
    *   *Simple Docs:* Delegates directly to **✍️ DocuCrafter (Cheap)**.
    *   *Complex Goal:* Delegates planning to **🏛️ Strategic Planner (Smart)**.
    *   *Design Task:* Delegates directly to **📐 Solution Architect (Smart)**.
    *   *Unclear:* Asks for clarification.
2.  **✍️ Planning (If Triggered):** The **🏛️ Strategic Planner (Smart)** creates/updates the plan in `project_overview.json`, defining tasks with `delegation_details` (including `suggested_mode` and `NNN:type:subject` `taskId`). Signals completion.
3.  **🚦 Coordination & Delegation (Execution Cycle):** The **🚦 Workflow Coordinator (Cheap)** starts its execution cycle:
    *   Reads `project_overview.json` to find 'Pending' tasks with met dependencies.
    *   For each ready task, reads `delegation_details` and the crucial `suggested_mode`.
    *   Updates task status to 'In Progress' (preparing for batch update).
    *   Delegates the task via `<new_task>` to the **exact mode suggested by the Planner** (e.g., `coder-monk`, `solution-architect`, `docu-crafter`). Associates platform task ID with project `taskId`.
4.  **🧑‍🧘‍♂️ Agent Execution & State Creation:**
    *   The delegated agent (e.g., `coder-monk`, `solution-architect`) executes its task.
    *   Creates output artifacts in designated `.state/` subdirectories (e.g., `.state/specs/`, code changes in `src/`).
    *   **Upon completion or failure**, the agent **creates and writes its own task state file** (`.state/tasks/{its_taskId}.json`) containing final status (`Done`, `Failed`, `Error`, `Validated`), output references, logs, and potentially `planned_subtasks` (Architect) or `validation_result_for_target` (Validator).
    *   The agent signals completion to the platform.
5.  **📊 Result Processing & State Validation:** Back in the Coordinator's cycle:
    *   It **waits for and receives a completion signal** from the platform for a delegated task, identifying the corresponding project `taskId`.
    *   It **reads the specific state file** created by the completed agent (`.state/tasks/{taskId}.json`).
    *   It **validates the structure** of the read JSON file.
    *   It extracts the final status and any relevant results (subtasks, validation outcomes) from the state file.
    *   It prepares a list of ALL changes needed for the overview file (status updates, adding new planned subtasks, updating validation results on target tasks).
6.  **💾 Batch Overview Update:** The Coordinator applies ALL prepared changes to `project_overview.json` in a **single batch `edit` operation**.
7.  **🔄 Iteration & Loop:** The Coordinator continues the cycle (Step 3) until the plan is complete or requires intervention/replanning.

*(Note: Validation (`guardian-validator`) is handled within this loop based on Planner's `suggested_mode`.)*

## 📊 Workflow Diagram

This ASCII diagram visualizes the `rooroo` v0.2.0 workflow: Triage & Delegation, Agent Execution & State Creation, and Coordinator Result Processing.

```text
+----------------------------------------------------------------------+ Phase 1: Triage & Initial Delegation +----------------------------------------------------------------------+

[User Input]
     |
     v
[🚦 Workflow Coordinator (Cheap)]
     |
     v
{Analyze Request & Triage Rules?}
     |--- (Simple Code/Debug) --> [Delegate Coder Monk] ---------+--> [To Phase 2]
     |--- (Simple Docs) -------> [Delegate DocuCrafter] ---------+--> [To Phase 2]
     |--- (Needs Planning) ----> [Delegate Planner (Smart)] ----+--> [Planner Creates/Updates project_overview.json] --> [Inform User]
     |--- (Needs Design) ------> [Delegate Architect (Smart)] ---+--> [To Phase 2]
     |--- (Execute/Status Plan) -> {Plan Exists?} --(Yes)--------> [Proceed to Phase 2]
     |                                           +--(No)-------> [Inform User No Plan]
     +--- (Ambiguous) ---------> [Ask Clarification]
                                 |
                                 v
                          (Back to Coordinator)

+----------------------------------------------------------------------+ Phase 2: Execution Cycle (Signal Driven) +----------------------------------------------------------------------+

[Start Cycle / From Phase 1]
     |
     v
[Coordinator Reads project_overview.json]
     |
     v
{Ready Tasks?} -----(Yes)-----> [Coordinator Delegates Task (via suggested_mode)] ----+--> [Associate Platform ID with TaskID]
     |                                                                                |
   (No)                                                                               v
     |                                                                    [Agent Executes Task] --> [Creates Artifacts in .state/... or src/...]
     v                                                                                |
[Coordinator Waits for Signal] <------------------------------------------------------ [Agent Creates OWN .state/tasks/{taskId}.json] <-- [Agent Signals Completion]
     |
     v
[Coordinator Reads Agent's .state/tasks/{taskId}.json]
     |
     v
[Coordinator Validates State File Structure]
     |
     v
[Coordinator Prepares Overview Update List (Status, Subtasks, Validation)]
     |
     v
[Coordinator Batch Writes Updates to project_overview.json]
     |
     v
(Back to Read project_overview.json - Loop)

```

## 🤖 Meet the Crew 🤖

* **🚦 Workflow Coordinator (Primary Interface / Cheap Model Recommended):** Performs rule-based triage. Manages plan execution by strictly following Planner's `suggested_mode`. Waits for completion signals, reads agent state files (`.state/tasks/`), validates them, and updates `project_overview.json` via batch edits. *Candidate for fast/cheap LLM.*
* **🏛️ Strategic Planner (On-Demand / Smart Model Recommended):** Invoked by Coordinator. Decomposes goals, creates/updates plan in `project_overview.json` (using `NNN:type:subject` IDs), defines `delegation_details` including `suggested_mode`. *Does not create task state files.* *Candidate for smart/expensive LLM.*
* **📐 Solution Architect (Smart Model Recommended):** Creates technical specifications (output to `.state/specs/`), potentially defines `planned_subtasks`. Creates **only** own task state file (`.state/tasks/{taskId}.json`) upon completion, including output paths and subtasks. *Candidate for smart/expensive LLM.*
* **🎨 UX Specialist (Smart Model Recommended):** Designs user flows/UI (output to `.state/design/`). Creates **only** own task state file (`.state/tasks/{taskId}.json`) upon completion, including output paths. *Candidate for smart/expensive LLM.*
* **🛡️ Guardian Validator (Cheap Model Recommended):** Executes validation/tests against a `target_task_id`. Creates reports (output to `.state/reports/`). Reports results (status, `validation_result_for_target`, report path) **only** in own task state file (`.state/tasks/{taskId}.json`). *Candidate for fast/cheap LLM.*
* **✍️ DocuCrafter (Cheap Model Recommended):** Generates/updates documentation (output to `.state/docs/`). Creates **only** own task state file (`.state/tasks/{taskId}.json`) upon completion, including output paths. *Candidate for fast/cheap LLM.*
* **🧘‍♂️ Coder Monk (Custom):** Executes coding/debugging/refactoring tasks based on instructions. Modifies files in the workspace. Creates **only** own task state file (`.state/tasks/{taskId}.json`) upon completion, reporting status (`Done`/`Failed`/`Error`) and modified file paths. *Model tier depends on task complexity.*

## 🚀 Get Started!  🚀

To use this `rooroo` agent team:

1.  **Install Roo Code:** Ensure the [Roo Code VS Code extension](https://marketplace.visualstudio.com/items?itemName=RooVeterinaryInc.roo-cline) is installed.
2.  **Override Local Modes:** Copy the latest `.roomodes` file (v0.2.0+) into your workspace root.
3.  **Reload VS Code:** Use `Ctrl+Shift+P` or `Cmd+Shift+P` -> "Developer: Reload Window".
4.  **Activate the Coordinator:** Open Roo Code chat, select **🚦 Workflow Coordinator (Cheap Model - Primary Interface)**.
5.  **State Your Goal:** Describe the project or task.
6.  **Coordinator Triage & Delegation:** The Coordinator performs triage. It may delegate planning to **🏛️ Strategic Planner (Smart)**, design to **📐 Solution Architect (Smart)**, documentation to **✍️ DocuCrafter (Cheap)**, or coding directly to **🧘‍♂️ Coder Monk**. Wait for the Planner if invoked.
7.  **Coordinator Executes Plan (Signal Driven):** Follow the Coordinator's lead as it delegates tasks based on the Planner's `suggested_mode`. Agents execute and create their own `.state/tasks/{taskId}.json` files upon completion. The Coordinator waits for signals, reads these files, validates them, and reports progress via batched updates to `project_overview.json`.
8.  **Review Artifacts & State:** Monitor progress via output directories within `.state/` (`.state/specs/`, `.state/design/`, etc.), individual task state files in `.state/tasks/`, and the central `project_overview.json`.

Let `rooroo` v0.2.0 bring **efficient, Coordinator-led, signal-driven orchestration** and **specialized expertise** to your AI development!