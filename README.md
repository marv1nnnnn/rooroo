# 🚀 rooroo (如如): Minimalist AI Orchestration with Specialist Agents 🚀

**Version: v0.4.1** | [Changelog](changelog.md) | [v0.4.0 Details](v0.4.0.md) | [v0.3.0 Details](v0.3.0.md) | [v0.2.0 Details](v0.2.0.md) | [v0.1.0 Details](v0.1.0.md)

`rooroo` provides **minimalist AI orchestration** for software development using **specialist agents** within VS Code via the [Roo Code extension](https://github.com/RooVetGit/Roo-Code). It employs a lean, coordinated team with distinct planning and execution phases, driven by a **Coordinator-led, signal-driven workflow** using **`task_queue.jsonl`** for task management and **`task_log.jsonl`** for event logging. The `Strategic Planner` manages the queue, while the `Workflow Coordinator` dispatches tasks and logs progress.

## 🤔 What's in a Name? The Meaning of "rooroo (如如)"

The name "rooroo" comes from the term **"如如" (rú rú)** found in Buddhist philosophy. It relates to the concept of **Tathātā**, often translated as "Thusness" or "Suchness."

![img](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQeUmsB4LIHLErFEbei5g8PfIFG-XQntgqLyA&s)

* **Thusness/Suchness (真如):** Refers to the fundamental, unchanging, true nature of all phenomena – reality as it is.
* **Equality & Non-Duality (不二平等):** Implies that, at the ultimate level, all things share this same essential nature.
* **Emphasis:** The repetition "如如" emphasizes that this inherent "thusness" is pervasive across *all* things.

In the context of this project, the name evokes the idea of an underlying, consistent nature guiding the orchestration. This philosophy informs the **minimalist approach**, focusing on the essential 'thusness' of each agent's specialized role.


## ✨ Key Principles

*   **Minimalism & Specialization:** A small team of agents with clear roles (Smart/Cheap tiers) avoids over-complexity.
*   **Coordinator-Led Orchestration:** An efficient, signal-driven workflow where the `Workflow Coordinator` triages new requests (delegating planning to the `Strategic Planner`), dispatches tasks from `task_queue.jsonl` based on the Planner's definitions, waits for completion signals, reads agent state files (`.state/tasks/*.json`), logs events to `task_log.jsonl`, and consumes tasks from the queue.
*   **Planner-Managed Queue:** The `Strategic Planner` has sole authority over `task_queue.jsonl`, including task creation, ID assignment, integration of new work, and managing refinement loops by modifying the queue.
*   **Decoupled State & Logging:** Agents manage their own state (`.state/tasks/*.json`), which the Coordinator reads. Key lifecycle events are logged to `task_log.jsonl` by the Coordinator and Planner.
*   **Consistent Task Management:** The `Strategic Planner` assigns final sequential IDs (`NNN#...`) to all tasks in `task_queue.jsonl`.
*   **Cost-Effectiveness:** Targeted use of Smart vs. Cheap LLMs per role, optimized through clear separation of concerns.
*   **Structured Workflow:** Defined roles, artifacts, and line-oriented data files promote clarity and robustness.

## 🚀 Get Started & Core Workflow

Follow these steps to use the `rooroo` agent team:

1.  **Install Roo Code:** Ensure the [Roo Code VS Code extension](https://marketplace.visualstudio.com/items?itemName=RooVeterinaryInc.roo-cline) is installed.
2.  **Override Local Modes:** Copy the latest `.roomodes` file (v0.4.0+) into your workspace root.
3.  **Reload VS Code:** Use `Ctrl+Shift+P` or `Cmd+Shift+P` -> "Developer: Reload Window".
4.  **(Optional) Brainstorming:** Activate **💡 Idea Sparker (Smart)** in Roo Code chat for interactive ideation. It can save summaries to `.state/brainstorming/`.
5.  **Activate Coordinator:** Select **🚦 Workflow Coordinator (Cheap)** in Roo Code chat.
6.  **State Your Goal:** Describe your project or task. You can reference brainstorming outputs.
7.  **Coordinator Triage & Planner Engagement:**
    *   The `Workflow Coordinator` analyzes your request.
    *   *Complex Goal/New Project/Significant Change:* Informs user and creates a temporary task in `task_queue.jsonl` for the **🏛️ Strategic Planner (Smart)**. The Planner analyzes the request, (re)plans the necessary tasks, assigns `NNN#` IDs, and (re)writes `task_queue.jsonl`. The Planner logs its actions to `task_log.jsonl`.
    *   *Smaller, Specific Coding/Docs/Test Task:* Informs user and creates a temporary task for the `Strategic Planner` to analyze, define, assign an ID, and integrate into `task_queue.jsonl`.
    *   The Coordinator waits for the Planner to complete its (re)planning task.
8.  **Coordinator Executes Plan (Queue Driven & Signal Driven):**
    *   The `Workflow Coordinator` reads the top task from `task_queue.jsonl`.
    *   If the queue is empty, the process stops. If a task is present but its `task_id` is malformed, the Coordinator logs a system error and awaits Planner intervention.
    *   It attempts to delegate the task to the agent specified in the task's `delegation_details.suggested_mode` using the `new_task` tool.
    *   **If delegation fails** (i.e., the `new_task` tool call is unsuccessful):
        *   The Coordinator logs a `system_error` to `task_log.jsonl` detailing the delegation failure.
        *   It informs the user about the failure.
        *   The task remains at the top of `task_queue.jsonl`, and the Coordinator stops processing this task, awaiting Planner review or other intervention.
    *   **If delegation succeeds:**
        *   The Coordinator logs the `task_delegated` event to `task_log.jsonl`, noting the `task_id` and the `delegated_to_mode`.
        *   It then updates `task_queue.jsonl` by removing the successfully dispatched task from the top of the queue.
        *   It informs the user that the task (e.g., `task_id` - 'description') has been delegated to the specified agent and is now awaiting completion.
    *   The designated agent executes the task, potentially creating artifacts (e.g., specs in `.state/specs/`, code changes in `src/`).
    *   **Crucially:** Upon completion/failure, the agent creates its own state file (`.state/tasks/{taskId}.json`) detailing its status, any outputs (like file references or sub-task proposals), and errors.
    *   The agent signals completion (or failure) to the platform.
9.  **Result Processing & Iteration:**
    *   The `Workflow Coordinator` receives the signal and reads the agent's `.state/tasks/{taskId}.json`.
    *   It logs the completion/failure event to `task_log.jsonl`.
    *   **Handling Failures:** If a task fails, the `Strategic Planner` is responsible for analyzing the failure (based on logs and agent state files, often triggered by a specific review task for the Planner) and deciding on the course of action (e.g., creating a refinement task, re-queueing, etc.) by modifying `task_queue.jsonl`.
    *   **Handling Success/Sub-tasks:** If an agent (like `Solution Architect`) proposes new sub-tasks in its state file (with `TEMP#...` IDs), a subsequent task for the `Strategic Planner` will handle their integration into `task_queue.jsonl` with final `NNN#...` IDs.
    *   The Coordinator informs the user of the task completion status.
    *   The cycle repeats with the Coordinator picking the next task from `task_queue.jsonl`.
10. **Review Artifacts:** Monitor progress via `.state/` subdirectories, individual `.state/tasks/*.json` files, `task_queue.jsonl`, and `task_log.jsonl`.

### Workflow Diagram

```text
+----------------------------------------------------------------------+ Phase 0: Optional Brainstorming +----------------------------------------------------------------------+

[User Initiates Brainstorming] -> [💡 Idea Sparker (Smart)] <-> [User] -> [Creates Optional Output in .state/brainstorming/]

+----------------------------------------------------------------------+ Phase 1: User Interaction & Triage (Coordinator) +----------------------------------------------------------------------+

[User Input (Goal/Request)] -> [🚦 Workflow Coordinator (Cheap)] -> {Triage}
    |
    |--- (New Project/Goal/Major Change/New Work Item) --> [Coordinator creates TEMP#plan#... task in task_queue.jsonl for Planner]
    |
    |--- (Status Request) --> [Coordinator Reads task_queue.jsonl & task_log.jsonl, Summarizes]
    |
    |--- ("Proceed"/"Run Next") --> [Coordinator proceeds to Phase 2]
    |
    +--- (Ambiguous) ---------> [Coordinator Asks Clarification]

+----------------------------------------------------------------------+ Phase 1.5: Planning & Queue Management (Planner) +----------------------------------------------------------------------+

[Planner consumes TEMP#plan#... task from task_queue.jsonl]
    |                                                       
    v                                                       
[🏛️ Strategic Planner (Smart)] -> [Analyzes Request/Sub-tasks/Failures] -> [Creates/Updates/Modifies task_queue.jsonl with NNN# tasks]
    |                                                       
    +--> [Logs planning actions to task_log.jsonl]
    |                                                       
    +--> [Signals its own TEMP#plan#... task completion]

+----------------------------------------------------------------------+ Phase 2: Task Dispatch & Execution Cycle (Coordinator & Agents) +----------------------------------------------------------------------+

[Coordinator checks task_queue.jsonl] --(Not Empty)--> [Coordinator Reads Top Task]
    |                                                                     |
   (Empty)                                                                v
    |                                         [Coordinator Logs Delegation to task_log.jsonl]
    v                                                                     |
[Process Ends or Awaits New Plan]           [Coordinator Updates task_queue.jsonl (removes top task)]
                                                                          |
                                                                          v
                                          [Coordinator Delegates NNN# Task to Suggested Agent]
                                                                          |
                                                                          v
                                                      [Agent Executes Task] -> [Creates Artifacts]
                                                                          |
                                          [Agent Creates .state/tasks/{taskId}.json] -> [Agent Signals Completion]
                                                                          |
                                                                          v
                                          [Coordinator Reads Agent's .state/tasks/{taskId}.json]
                                                                          |
                                                                          v
                                          [Coordinator Logs Completion/Failure to task_log.jsonl] -> [Inform User]
                                                                          |
                                                                          +---- (Loop back to Read task_queue.jsonl for next task)
```

## 🤖 The Agent Team & Cost Optimization

`rooroo` uses specialized agents, allowing for cost optimization by assigning appropriate LLM tiers:

*   **🚦 Workflow Coordinator (⚡ Cheap/Fast Recommended):** Primary interface, rule-based triage (delegating planning/integration to Planner), consumes tasks from `task_queue.jsonl`, delegates to agents, reads/processes agent state files, logs all its key actions to `task_log.jsonl`.
*   **🏛️ Strategic Planner (🧠 Smart/Expensive Recommended):** Sole authority for creating and managing `task_queue.jsonl`. Decomposes goals, integrates new tasks/sub-tasks, assigns all final `NNN#` IDs, manages error handling and refinement strategies by modifying the queue. Logs its planning actions to `task_log.jsonl`.
*   **📐 Solution Architect (🧠 Smart/Expensive Recommended):** Creates technical specs (`.state/specs/`), potentially defines sub-tasks (using `TEMP#...` IDs in its state file for the Planner to integrate). Handles refinement tasks assigned by the Planner. Creates its own task state file (`.state/tasks/{taskId}.json`).
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
├── task_queue.jsonl          # Definitive task queue (Managed by Strategic Planner, Consumed by Coordinator)
├── task_log.jsonl            # Append-only log of key workflow events (Written by Coordinator & Planner)
│
└── src/                      # Example source code directory (Modified by Coder Monk)
    └── ...
```

## 📊 Core Data Files

### `task_queue.jsonl`

This file contains the ordered list of tasks to be executed. Each line is a JSON object representing one task. The `Strategic Planner` is responsible for its content and structure.

Example task line structure (defined by Planner):
```json
{"task_id": "010#chore#initial_setup", "delegation_details": {"description": "Set up the initial project structure.", "suggested_mode": "coder-monk", "context": {}, "acceptance_criteria": "Project directory created with basic files."}, "dependencies_original": [], "priority": 10, "added_to_queue_at": "2024-07-25T10:00:00Z"}
```
Key fields per task object:
*   `task_id`: Unique identifier (`NNN#type#subject` format), assigned by the `Strategic Planner`.
*   `delegation_details`: Object containing information for the agent executing the task, including:
    *   `description`: What needs to be done.
    *   `suggested_mode`: The agent mode the Planner suggests for this task.
    *   `context`: Any additional information or data required for the task.
    *   `acceptance_criteria`: How to determine if the task is successfully completed.
*   `dependencies_original`: Array of `task_id`s that this task depends on (for Planner's reference; execution order is primarily dictated by queue order).
*   `priority`: A number indicating task priority (for Planner's reference in ordering).
*   `added_to_queue_at`: ISO 8601 timestamp of when the task was added/last significantly updated in the queue by the Planner.

### `task_log.jsonl`

This append-only file records key events in the workflow. Each line is a JSON object representing one log entry. Both the `Workflow Coordinator` and `Strategic Planner` write to this file.

Example log entry structure:
```json
{"log_id": "a1b2c3d4-e5f6-7890-1234-567890abcdef", "timestamp": "2024-07-25T10:00:05Z", "event_type": "task_delegated", "task_id": "010#chore#initial_setup", "details": {"delegated_to_mode": "coder-monk"}, "actor_mode": "workflow-coordinator"}
{"log_id": "b2c3d4e5-f6a7-8901-2345-67890abcdef0", "timestamp": "2024-07-25T10:05:00Z", "event_type": "task_completed", "task_id": "010#chore#initial_setup", "details": {"agent_status_reported": "Done", "error_message": null, "agent_state_file_ref": ".state/tasks/010#chore#initial_setup.json", "output_references": ["src/main.py"]}, "actor_mode": "workflow-coordinator"}
{"log_id": "c3d4e5f6-a7b8-9012-3456-7890abcdef01", "timestamp": "2024-07-25T09:55:00Z", "event_type": "plan_updated", "task_id": "PROJECT_GOAL_INITIAL_PLAN", "details": {"description": "Initial plan created for new web service goal", "tasks_added_count": 5}, "actor_mode": "strategic-planner"}
```
Key fields per log entry:
*   `log_id`: Unique identifier for the log entry (e.g., UUID).
*   `timestamp`: ISO 8601 timestamp of the event.
*   `event_type`: Type of event (e.g., `task_delegated`, `task_completed`, `task_failed`, `plan_updated`, `task_integrated_into_queue`, `refinement_initiated`, `system_error`).
*   `task_id`: The `task_id` relevant to this event (can be a specific task ID or a more general identifier for plan-level events).
*   `details`: An object containing event-specific information.
*   `actor_mode`: The agent mode that generated this log entry (e.g., `workflow-coordinator`, `strategic-planner`).

Let `rooroo` bring efficient, specialized, and configurable AI orchestration to your development workflow!