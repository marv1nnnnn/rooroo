# 🚀 rooroo (如如): Minimalist AI Orchestration with Specialist Agents 🚀

**Version: v0.3.2** | [Changelog](changelog.md) | [v0.3.0 Details](v0.3.0.md) | [v0.2.0 Details](v0.2.0.md) | [v0.1.0 Details](v0.1.0.md)

`rooroo` provides **minimalist AI orchestration** for software development using **specialist agents** within VS Code via the [Roo Code extension](https://github.com/RooVetGit/Roo-Code). It employs a lean, coordinated team with distinct planning and execution phases, driven by a **Coordinator-led, signal-driven workflow** that includes **automated refinement loops** for handling task failures due to insufficient specification.

## 🤔 What's in a Name? The Meaning of "rooroo (如如)"

The name "rooroo" comes from the term **"如如" (rú rú)** found in Buddhist philosophy. It relates to the concept of **Tathātā**, often translated as "Thusness" or "Suchness."

![img](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQeUmsB4LIHLErFEbei5g8PfIFG-XQntgqLyA&s)

* **Thusness/Suchness (真如):** Refers to the fundamental, unchanging, true nature of all phenomena – reality as it is.
* **Equality & Non-Duality (不二平等):** Implies that, at the ultimate level, all things share this same essential nature.
* **Emphasis:** The repetition "如如" emphasizes that this inherent "thusness" is pervasive across *all* things.

In the context of this project, the name evokes the idea of an underlying, consistent nature guiding the orchestration. This philosophy informs the **minimalist approach**, focusing on the essential 'thusness' of each agent's specialized role.


## ✨ Key Principles

*   **Minimalism & Specialization:** A small team of agents with clear roles (Smart/Cheap tiers) avoids over-complexity.
*   **Coordinator-Led Orchestration:** An efficient, signal-driven workflow where the Coordinator triages tasks, follows the Planner's `suggested_mode`, waits for completion signals, reads agent state files (`.state/tasks/*.json`), **validates state files against a schema**, performs batch updates to the central overview (`project_overview.json`), and **manages automated refinement loops** for tasks failing due to underspecification.
*   **Decoupled State & Central Config:** Agents manage their own state (`.state/tasks/*.json`), which the Coordinator reads and validates. The Planner defines project-wide `project_configuration`, and the Coordinator injects it into tasks, preventing configuration drift.
*   **Consistent Task Management:** The Coordinator assigns final sequential IDs (`NNN#...`) to all tasks, including sub-tasks initially proposed with temporary IDs (`TEMP#...`) and refinement tasks (`NNN#chore#refine_...`).
*   **Cost-Effectiveness:** Targeted use of Smart vs. Cheap LLMs per role, optimized through decoupled state, batch updates, and automated refinement.
*   **Structured Workflow:** Defined roles, artifacts, and optional DDD/TDD focus promote clarity.

## 🚀 Get Started & Core Workflow

Follow these steps to use the `rooroo` agent team:

1.  **Install Roo Code:** Ensure the [Roo Code VS Code extension](https://marketplace.visualstudio.com/items?itemName=RooVeterinaryInc.roo-cline) is installed.
2.  **Override Local Modes:** Copy the latest `.roomodes` file (v0.3.0+) into your workspace root.
3.  **Reload VS Code:** Use `Ctrl+Shift+P` or `Cmd+Shift+P` -> "Developer: Reload Window".
4.  **(Optional) Brainstorming:** Activate **💡 Idea Sparker (Smart)** in Roo Code chat for interactive ideation. It can save summaries to `.state/brainstorming/`.
5.  **Activate Coordinator:** Select **🚦 Workflow Coordinator (Cheap)** in Roo Code chat.
6.  **State Your Goal:** Describe your project or task. You can reference brainstorming outputs (e.g., "Based on `.state/brainstorming/session_summary.md`, implement...").
7.  **Coordinator Triage & Delegation:**
    *   The Coordinator analyzes your request.
    *   *Simple Code/Docs:* Delegates directly to Coder Monk or DocuCrafter.
    *   *Complex Goal:* Delegates planning to the **🏛️ Strategic Planner (Smart)**, which creates/updates `project_overview.json` with tasks (including `suggested_mode`) and optional `project_configuration`.
    *   *Design Task:* Delegates to the **📐 Solution Architect (Smart)**.
8.  **Coordinator Executes Plan (Signal Driven):**
    *   The Coordinator reads `project_overview.json` for pending tasks.
    *   It delegates tasks to the agent specified by `suggested_mode`, injecting `project_configuration` if defined.
    *   The designated agent executes the task, potentially creating artifacts (e.g., specs in `.state/specs/`, code changes in `src/`).
    *   **Crucially:** Upon completion/failure, the agent creates its own state file (`.state/tasks/{taskId}.json`) detailing status, outputs, errors (potentially signaling insufficient specification), and any new sub-tasks (using temporary `TEMP#...` IDs).
    *   The agent signals completion.
9.  **Result Processing & Iteration:**
    *   The Coordinator receives the signal and reads the agent's `.state/tasks/{taskId}.json`.
    *   It validates the state file **against its schema**.
    *   **Handling Failures:**
        *   If the state indicates failure due to *insufficient specification*, the Coordinator initiates a **refinement loop**: generates a `NNN#chore#refine_...` task, delegates it to the `Solution Architect` to improve the original task's details, processes the refinement result, updates the original task, and resets it to `Pending`.
        *   For other failures, the Coordinator updates the task status and error details in the overview.
    *   **Handling Success/Sub-tasks:**
        *   If successful, updates status.
        *   Assigns final sequential IDs (`NNN#...`) to any temporary sub-task IDs (`TEMP#...` or the refinement task ID).
    *   It prepares a list of all necessary updates (status changes, new tasks, error details, refined task details).
    *   It applies all updates to `project_overview.json` in a **single batch operation**.
    *   The cycle repeats until the plan is complete.
10. **Review Artifacts:** Monitor progress via `.state/` subdirectories, individual `.state/tasks/*.json` files, and the central `project_overview.json`.

### Workflow Diagram

```text
+----------------------------------------------------------------------+ Phase 0: Optional Brainstorming +----------------------------------------------------------------------+

[User Initiates Brainstorming] -> [💡 Idea Sparker (Smart)] <-> [User] -> [Creates Optional Output in .state/brainstorming/]

+----------------------------------------------------------------------+ Phase 1: Triage & Initial Delegation +----------------------------------------------------------------------+

[User Input (Goal)] -> [🚦 Workflow Coordinator (Cheap)] -> {Triage}
    |--- (Simple Code/Debug) --> [Delegate Coder Monk] ----+
    |--- (Simple Docs) -------> [Delegate DocuCrafter] ----+
    |--- (Needs Planning) ----> [Delegate Planner (Smart)] -> [Planner Creates/Updates project_overview.json] -> [Inform User]
    |--- (Needs Design) ------> [Delegate Architect (Smart)]+
    |--- (Execute Plan) ------> [Proceed to Phase 2]
    +--- (Ambiguous) ---------> [Ask Clarification]

+----------------------------------------------------------------------+ Phase 2: Execution Cycle (Signal Driven) +----------------------------------------------------------------------+

[Coordinator Reads project_overview.json] -> {Ready Tasks?} --(Yes)--> [Coordinator Delegates Task + Injects project_config]
    |                                                                     |
   (No)                                                                   v
    |                                                      [Agent Executes Task] -> [Creates Artifacts]
    v                                                                     |
[Coordinator Waits for Signal] <----------------------- [Agent Creates .state/tasks/{taskId}.json] -> [Agent Signals Completion]
    |
    v
[Coordinator Reads Agent's .state/tasks/{taskId}.json] -> [Validate State] -> {Process Status}
                                                                |         |---(Failed: Insufficient Spec?)-->(Yes)--> [Create & Delegate Refinement Task (to Architect)] -> [Architect Creates Refinement State] -> [Coordinator Processes Refinement State] -> [Update Original Task Details & Status=Pending]
                                                                |         |
                                                                |         +---(Failed: Other / Error)-----> [Update Task Status/Error]
                                                                |         |
                                                                |         +---(Success / Validated)-------> [Update Task Status]
                                                                |
                                                                +--- {New TEMP# Tasks?} --(Yes)--> [Assign Final IDs to TEMP# tasks]
                                                                |
                                                                v
                                                  [Prepare Overview Update List] -> [Batch Write Updates to project_overview.json] -> (Loop back to Read project_overview.json)
```

## 🤖 The Agent Team & Cost Optimization

`rooroo` uses specialized agents, allowing for cost optimization by assigning appropriate LLM tiers:

*   **🚦 Workflow Coordinator (⚡ Cheap/Fast Recommended):** Primary interface, rule-based triage, signal-driven execution, reads/validates state files, injects config, **manages refinement loops**, batch updates overview.
*   **🏛️ Strategic Planner (🧠 Smart/Expensive Recommended):** Decomposes goals, creates/updates `project_overview.json` with tasks, `suggested_mode`, and optional `project_configuration`. Does *not* create task state files.
*   **📐 Solution Architect (🧠 Smart/Expensive Recommended):** Creates technical specs (`.state/specs/`), potentially defines sub-tasks (`TEMP#...`), **handles refinement tasks** (updating specifications based on feedback). Creates its own task state file (`.state/tasks/{taskId}.json`).
*   **🎨 UX Specialist (🧠 Smart/Expensive Recommended):** Designs UI/UX (`.state/design/`). Creates its own task state file.
*   **🛡️ Guardian Validator (⚡ Cheap/Fast Recommended):** Executes tests/validation. Creates reports (`.state/reports/`) and its own task state file.
*   **✍️ DocuCrafter (⚡ Cheap/Fast Recommended):** Generates/updates docs (`.state/docs/`). Creates its own task state file.
*   **🧘‍♂️ Coder Monk (Custom / Varies):** Executes coding/debugging. Modifies workspace files. **Signals need for refinement** if specs are insufficient via its state file. Creates its own task state file. Model tier depends on complexity.
*   **💡 Idea Sparker (🧠 Smart/Expensive Recommended):** Interactive brainstorming partner, operates conversationally outside the main workflow.

*Configure the underlying LLM for each agent mode (if supported) to balance cost and capability.*

## 📁 Directory Structure

```
<Project Root>/
├── .state/                   # Internal state managed by workflow
│   ├── brainstorming/        # Output from Idea Sparker (Optional)
│   ├── tasks/                # Individual task state files (CREATED by agents)
│   │   ├── 010#chore#setup_project.json
│   │   └── ...
│   ├── specs/                # Output from Solution Architect
│   ├── design/               # Output from UX Specialist
│   ├── docs/                 # Output from DocuCrafter
│   └── reports/              # Output from Guardian Validator
│
├── project_overview.json     # Central plan & task list (Managed by Coordinator & Planner)
│
└── src/                      # Example source code directory (Modified by Coder Monk)
    └── ...
```

## 📊 Project Overview Structure (`project_overview.json`)

This file contains the plan and task list, potentially including project-wide settings:

```json
{
  "project_name": "Example API",
  "goal": "Build a RESTful API",
  "project_configuration": { // Optional: Defined by Planner, Injected by Coordinator
    "python_runner": "uv",
    "test_framework": "pytest",
    // ... other settings
  },
  "tasks": [
    {
      "taskId": "010#chore#setup_project",
      "status": "Done",
      "delegation_details": {
        "suggested_mode": "coder-monk",
        // ... other details
      }
      // ... other task properties
    },
    // ... more tasks
  ]
}
```

Let `rooroo` bring efficient, specialized, and configurable AI orchestration to your development workflow!