# 🚀 rooroo (如如): Minimalist AI Orchestration with Specialist Agents 🚀

**Version: v0.5.5** | [Changelog](changelog.md) | [v0.5.0 Details](v0.5.0.md) | [v0.4.0 Details](v0.4.0.md) | [v0.3.0 Details](v0.3.0.md) | [v0.2.0 Details](v0.2.0.md) | [v0.1.0 Details](v0.1.0.md)

`rooroo` provides **minimalist AI orchestration** for software development using **specialist Rooroo agents** within VS Code via the [Roo Code extension](https://github.com/RooVetGit/Roo-Code). It employs a lean, coordinated team with distinct planning and execution phases, driven by a **`Rooroo Navigator`-led, Output Envelope-based workflow**. Task management relies on `.rooroo/queue.jsonl`, event logging on `.rooroo/logs/activity.jsonl`, detailed task briefings are provided in `.rooroo/tasks/TASK_ID/context.md`, and agent-produced artifacts are stored directly in `.rooroo/tasks/TASK_ID/`. All file paths are relative to the VS Code workspace root, with Rooroo internal files prefixed by `.rooroo/`.

## 🤔 What's in a Name? The Meaning of "rooroo (如如)"

The name "rooroo" comes from the term **"如如" (rú rú)** found in Buddhist philosophy. It relates to the concept of **Tathātā**, often translated as "Thusness" or "Suchness."

![img](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQeUmsB4LIHLErFEbei5g8PfIFG-XQntgqLyA&s)

* **Thusness/Suchness (真如):** Refers to the fundamental, unchanging, true nature of all phenomena – reality as it is.
* **Equality & Non-Duality (不二平等):** Implies that, at the ultimate level, all things share this same essential nature.
* **Emphasis:** The repetition "如如" emphasizes that this inherent "thusness" is pervasive across *all* things.

In the context of this project, the name evokes the idea of an underlying, consistent nature guiding the orchestration. This philosophy informs the **minimalist approach**, focusing on the essential 'thusness' of each agent's specialized role.


## ✨ Key Principles

*   **Minimalism & Specialization:** A small team of Rooroo agents with clear roles, leveraging appropriate LLM tiers. Adherence to the unified workspace-relative path handling system ensures clarity in file operations: all paths are relative to the VS Code workspace root, and Rooroo internal files are prefixed with `.rooroo/`.
*   **Navigator-Led Orchestration & Resilience:** An efficient, interactive workflow where the **🧭 Rooroo Navigator** serves as the primary user interface and central coordinator. It embodies core principles of evidence-based operation, proactive logging, resilience, project integrity, and adherence to process. It engages the `rooroo-planner` for complex work, dispatches tasks from `.rooroo/queue.jsonl` (paths within are workspace-relative), processes agent responses (structured JSON "Output Envelopes" which must be strictly parsable), logs events with severity to `.rooroo/logs/activity.jsonl` (paths within are workspace-relative) using its `SafeLogEvent` procedure (which involves escaped JSON), and guides the user through decisions and failures, potentially halting on critical, unrecoverable errors. The Navigator employs detailed internal `<thinking>` blocks to manage its complex logic (such as queue parsing, variable state management, and message construction for experts) while maintaining concise user-facing communication. All path references in its operations and instructions adhere to the workspace-relative convention.
*   **Planner-Managed Queue & Context:** The `rooroo-planner` has sole authority over creating planned tasks for `.rooroo/queue.jsonl` and creates detailed task briefings in `.rooroo/tasks/TASK_ID/context.md` for each sub-task. These contexts and goals clearly use workspace-relative paths for both user project files and Rooroo artifact paths (which start with `.rooroo/` and point directly to artifacts in other task folders, e.g., `.rooroo/tasks/PREVIOUS_TASK_ID/artifact.ext`).
*   **Output Envelope-Based Communication:** Agents report status, results, or ask questions by returning a structured, strictly parsable JSON string "Output Envelope" directly to the `Rooroo Navigator`. All paths within these envelopes are workspace-relative, with Rooroo artifacts referenced by their direct paths within `.rooroo/tasks/TASK_ID/`.
*   **Structured Artifacts:** All task-related files (context briefings, agent-produced artifacts) are organized within a clear `.rooroo/tasks/TASK_ID/` structure, with paths being workspace-relative.
*   **Consistent Task IDs:** All tasks use the `ROO#` prefix (e.g., `ROO#PLAN_...`, `ROO#TEMP_...`, `ROO#SUB_...`) for unique identification throughout the system.
*   **Cost-Effectiveness:** Targeted use of Smart vs. Cheap LLMs per role.
*   **Structured Workflow:** Defined roles, artifacts, communication protocols, and line-oriented data files promote clarity and robustness.

## 🚀 Get Started & Core Workflow

Follow these steps to use the `rooroo` agent team (v0.5.5+):

1.  **Install Roo Code:** Ensure the [Roo Code VS Code extension](https://marketplace.visualstudio.com/items?itemName=RooVeterinaryInc.roo-cline) is installed.
2.  **Load Modes:** Ensure your `.roomodes` file (v0.5.5 compatible, defining the `rooroo-...` agents) is in your workspace root.
3.  **Reload VS Code:** Use `Ctrl+Shift+P` or `Cmd+Shift+P` -> "Developer: Reload Window".
4.  **(Optional) Brainstorming:** Activate **💡 Rooroo Idea Sparker** in Roo Code chat for interactive ideation. It can save summaries to `.rooroo/brainstorming/` or as artifacts within a task (`.rooroo/tasks/TASK_ID/rooroo-idea-sparker_summary.md`) if directed by the `Rooroo Navigator` or `rooroo-planner` as part of a task.
5.  **Activate Navigator:** Select **🧭 Rooroo Navigator** in Roo Code chat.
6.  **State Your Goal:** Describe your project or task to the Navigator.
7.  **Navigator Triage & Planner Engagement:**
    *   The `Rooroo Navigator` analyzes your request. It understands that all file references (both to your project files and its own operational files under `.rooroo/`) use paths relative to your VS Code workspace root. It utilizes extensive internal `<thinking>` blocks to process information and decide on next steps, ensuring its communication to you remains concise.
    *   *Complex Goal/New Project/Significant Change:* Informs you and prepares a message for the **🗓️ Rooroo Planner**. It invokes the Planner using `new_task` with a `ROO#PLAN_` task ID and a workspace-relative path to a `context.md` (e.g., `.rooroo/tasks/ROO#PLAN_.../context.md`) it creates for the Planner. This `context.md` will reference user project files using their workspace-relative paths (e.g., `src/main.py`) and any prior Rooroo artifacts using their direct task paths (e.g., `.rooroo/tasks/PREVIOUS_ROO_TASK_ID/artifact.ext`).
    *   The `rooroo-planner` analyzes the request (from its `context.md`), (re)plans the necessary sub-tasks, assigns `ROO#SUB_` IDs, creates `.rooroo/tasks/SUB_TASK_ID/context.md` files for each sub-task (workspace-relative path, e.g., `.rooroo/tasks/SUB_TASK_ID/context.md`), and generates an overview plan document. The sub-task `context.md` files will contain goals and references pointing to user project files (workspace-relative) and Rooroo artifacts (workspace-relative, e.g., `.rooroo/tasks/PREVIOUS_SUB_TASK_ID/artifact.ext`, pointing directly to the artifact).
    *   The Planner returns its `PlannerOutput` JSON envelope (strictly parsable) to the Navigator. This envelope contains the `queue_tasks_json_lines` for the sub-tasks (with all paths workspace-relative) and a summary message.
    *   The Navigator processes this envelope, adds the sub-tasks to `.rooroo/queue.jsonl`, logs the event using `SafeLogEvent` (with escaped JSON and workspace-relative paths), and informs you. Its internal processing of the planner's report and queue updating involves detailed steps within `<thinking>` blocks.
    *   *Simple Task:* If the Navigator deems the task simple, it may create a `ROO#TEMP_` task, write its `context.md` (e.g., `.rooroo/tasks/ROO#TEMP_.../context.md`, which will correctly reference user files by workspace-relative paths if needed), and dispatch it. Tool calls made by the Navigator for simple tasks will also use workspace-relative paths. The Navigator uses `<thinking>` blocks to manage the execution of simple tasks.
8.  **Navigator Executes Plan (Queue Driven & Envelope Driven):**
    *   The `Rooroo Navigator` reads the top task from `.rooroo/queue.jsonl`. This process, including parsing the queue file content, extracting task details, and preparing the remaining queue, is managed internally with detailed logic within `<thinking>` blocks, ensuring concise updates to you.
    *   If the queue is empty, it informs you and awaits further instruction (Phase 4).
    *   It prepares a message for the sub-agent, including the `ROO#` `taskId`, `context_file_path` (e.g., `.rooroo/tasks/TASK_ID/context.md`, a workspace-relative path), and the `goal_for_expert`. The `goal_for_expert` and the `context.md` will adhere to the workspace-relative path convention. The construction of this message is also detailed within the Navigator's `<thinking>` blocks.
    *   It invokes the designated Rooroo expert agent (e.g., `rooroo-developer`) using `new_task`.
    *   The expert agent executes the task, reading its briefing from the `context.md` file. When accessing user project files, it uses workspace-relative paths. It creates Rooroo-generated artifacts directly in its task folder: `.rooroo/tasks/TASK_ID/` (e.g., `.rooroo/tasks/TASK_ID/output_file.py`).
    *   **Crucially:** Upon completion, failure, or needing clarification, the agent returns its entire output as a structured, strictly parsable JSON **Output Envelope** directly to the Navigator. Output artifact paths in the envelope will be specified as workspace-relative paths (e.g., `src/modified_file.py` for modified user files, `.rooroo/tasks/TASK_ID/new_artifact.json` for new Rooroo artifacts stored directly in the task folder).
9.  **Result Processing & Iteration (Navigator - Phase 3 of its logic):**
    *   The `Rooroo Navigator` receives and parses the expert's JSON Output Envelope. This parsing and the subsequent logic for handling the report (clarification, completion, failure) are managed through detailed steps within its internal `<thinking>` blocks.
    *   **If `status` is `"NeedsClarification"`:**
        *   The Navigator presents the agent's question to you using `ask_followup_question`.
        *   Your response is relayed back to the expert agent for resumption (Navigator prepares a RESUME_TASK message and calls `new_task` for the same agent).
    *   **If `status` is `"Done"` or `"Failed"`:**
        *   The Navigator logs the event (e.g., `EXPERT_REPORT`) using its `SafeLogEvent` procedure to `.rooroo/logs/activity.jsonl`.
        *   It updates `.rooroo/queue.jsonl` by removing the processed task (by writing the remaining queue content back to the file, a process detailed in its `<thinking>` blocks).
        *   It informs you of the outcome, including links to any artifacts (which are stored directly in `.rooroo/tasks/TASK_ID/`).
        *   **Handling Agent-Reported Failures:** If status is `"Failed"`, the Navigator will inform you and guide you to Phase 4 for a decision (e.g., retry, skip, ask Planner to review).
    *   If the queue has more tasks and the previous task was `"Done"`, the cycle repeats (Navigator proceeds to Phase 2 to dispatch the next task).
10. **Review Artifacts:** Monitor progress via `.rooroo/` subdirectories, `.rooroo/queue.jsonl`, and `.rooroo/logs/activity.jsonl`. Rooroo-generated artifacts are found directly in `.rooroo/tasks/TASK_ID/`.

### Workflow Diagram

```text
+----------------------------------------------------------------------+ Phase 0: Optional Brainstorming +----------------------------------------------------------------------+

[User Initiates Brainstorming with 💡 Rooroo Idea Sparker]
(Can be tasked by Navigator/Planner as part of a ROO# task)

+----------------------------------------------------------------------+ Phase 1: User Interaction & Task Triage (Navigator) +----------------------------------------------------------------------+

[User Input (Goal/Request)] -> [🧭 Rooroo Navigator] -> {Triage}
    |
    |--- (Complex Work) --> [Navigator informs User, creates ROO#PLAN_ task, writes context.md for Planner]
    |                                | (Navigator calls `new_task` (mode: 'rooroo-planner'))
    |                                v
    |      [Planner returns JSON Output Envelope (PlannerOutput) with queue_tasks_json_lines -> Navigator processes, adds to .rooroo/queue.jsonl, logs, informs User]
    |
    |--- (Simple Task) --> [Navigator creates ROO#TEMP_ task, writes context.md, calls `new_task` for target Rooroo expert]
    |                                | (Expert returns JSON Output Envelope -> Navigator processes, logs, informs User)
    |
    |--- (Status Request) --> [Navigator Reads .rooroo/logs/activity.jsonl, Summarizes for User]
    |
    +--- (User says "Proceed" or task completes successfully and queue is not empty) --> [Navigator proceeds to Phase 2]
    |
    +--- (Queue empty or decision needed) --> [Navigator proceeds to Phase 4]

+----------------------------------------------------------------------+ Phase 2: Queued Task Dispatch (Navigator) +----------------------------------------------------------------------+

[Navigator checks .rooroo/queue.jsonl] --(Not Empty)--> [Navigator Reads Top Task (gets ROO# taskId, suggested_mode, context_file_path, goal_for_expert)]
    |                                                                     |
   (Empty)                                                                v
    |                                         [Navigator prepares message for Rooroo Expert (COMMAND: EXECUTE_TASK, ROO# TASK_ID, CONTEXT_FILE, GOAL)]
    v                                                                     | (Navigator calls `new_task` (mode: suggested_mode))
[Navigator informs User queue is empty, moves to Phase 4]                 v
                                                      [Rooroo Expert Executes Task (reads .rooroo/tasks/TASK_ID/context.md, creates artifacts in .rooroo/tasks/TASK_ID/)]
                                                                          | (Expert returns JSON Output Envelope to Navigator)
                                                                          v
                                          [Navigator Parses Envelope (Proceed to Phase 3)]

+----------------------------------------------------------------------+ Phase 3: Rooroo Expert Report Processing (Navigator) +----------------------------------------------------------------------+

[Navigator receives Rooroo Expert JSON Output Envelope]
    |
    |--- (`status: "NeedsClarification"`) --> [Navigator uses `ask_followup_question` with User] --> [User Response] --> [Navigator relays to Expert for RESUME_TASK] --> (Expert Resumes, returns new Envelope, loop to Phase 3)
    |
    |--- (`status: "Done"` or `"Failed"`) --> [Navigator logs to .rooroo/logs/activity.jsonl via SafeLogEvent]
    |                                                  |
    |                                                  +-- [Navigator updates .rooroo/queue.jsonl (removes processed task)]
    |                                                  |
    |                                                  +-- (Navigator informs User of outcome, linking artifacts)
    |                                                  |
    |                                                  +-- (If "Failed") --> [Navigator informs User, moves to Phase 4 for decision]
    |                                                  |
    |                                                  +-- (If "Done" & queue has tasks) --> [Loop to Phase 2 for next task]
    |                                                  |
    |                                                  +-- (If "Done" & queue empty) --> [Navigator informs User, moves to Phase 4]

+----------------------------------------------------------------------+ Phase 4: User Decision Point (Navigator) +----------------------------------------------------------------------+

[Navigator reaches decision point (e.g., plan ready, task failed, queue empty, clarification needed)]
    |
    v
[Navigator uses `ask_followup_question` to prompt User for next action (e.g., "Proceed with queue?", "How to handle failure?", "New goal?")]
    |
    v
[Based on User choice, loop to appropriate Phase or await new input]
```

## 🤖 The Agent Team & Cost Optimization

`rooroo` v0.5.5 uses specialized Rooroo agents. All agents operate under their directives, which primarily emphasize clearer internal `<thinking>` processes for the Navigator and a simplified, direct artifact storage path for all agents. This allows for cost optimization by assigning appropriate LLM tiers.

*   **🧭 Rooroo Navigator (⚡ Cheap/Fast Recommended):** Your primary interface and project guide. Manages user interaction with a direct and formal communication style. Operates based on **Core Principles**: Evidence-Based Operation, Proactive Logging, Resilience & Recovery, Project Integrity, and Guardian of the Process. Triages requests, understanding the unified workspace-relative path system for all file references. Delegates complex work to `rooroo-planner`, simple tasks directly using `ROO#TEMP_` IDs. Dispatches tasks from `.rooroo/queue.jsonl` by invoking other Rooroo experts. Processes their strictly parsable JSON "Output Envelope" responses. Logs events to `.rooroo/logs/activity.jsonl` (using its `SafeLogEvent` procedure with escaped JSON, workspace-relative paths, and **event severity levels** like INFO, WARN, ERROR, CRITICAL). Handles user decisions, especially for failures or when the queue is empty, using `ask_followup_question`. Implements a **`HandleCriticalErrorOrHalt` protocol** for unrecoverable system errors, which stops automated processing and informs the user. Critically, the Navigator uses extensive internal `<thinking>` blocks to manage its complex operational logic (like detailed queue parsing, variable state management, dynamic message construction for experts, and report processing) while ensuring all user-facing communication remains concise and to the point. All its operations and instructions to other agents enforce the workspace-relative path convention.
*   **🗓️ Rooroo Planner (🧠 Smart/Expensive Recommended):** Receives directives from the Navigator. Designs project plans, assigning `ROO#SUB_` task IDs. Creates detailed context Markdown files for each sub-task in `.rooroo/tasks/SUB_TASK_ID/context.md` (workspace-relative path). These contexts clearly specify goals and reference user project files (workspace-relative paths) and Rooroo artifacts (workspace-relative paths, e.g., `.rooroo/tasks/PREVIOUS_TASK_ID/artifact.ext`, pointing directly to artifacts in other task folders). Reports back to the Navigator via a strictly parsable JSON `PlannerOutput` envelope containing `queue_tasks_json_lines` (with all paths workspace-relative).
*   **🧑‍💻 Rooroo Developer (Custom / Varies):** Receives `ROO#` task ID, `context_file_path` (workspace-relative, e.g., `.rooroo/tasks/TASK_ID/context.md`), and `goal` from the Navigator. Executes coding tasks based on its `context.md`. Accesses user project files using workspace-relative paths. Rooroo-generated artifacts (e.g., `rooroo-developer_output.log` or `implemented_feature.py`) are stored directly in `.rooroo/tasks/TASK_ID/` (workspace-relative path) or modifies project files at their workspace-relative paths. Reports back via a strictly parsable JSON Output Envelope, with artifact paths specified as workspace-relative (pointing to direct storage in `.rooroo/tasks/TASK_ID/` for Rooroo artifacts).
*   **📊 Rooroo Analyzer (⚡ Cheap/Fast Recommended):** Receives `ROO#` task ID, `context_file_path` (workspace-relative), and `goal`. Performs analysis based on `context.md`, accessing user project data via workspace-relative paths. Generates reports/findings (e.g., `analysis_report.md`) directly in `.rooroo/tasks/TASK_ID/` (workspace-relative path). Reports back via a strictly parsable JSON Output Envelope with workspace-relative paths (pointing to direct storage in `.rooroo/tasks/TASK_ID/`).
*   **✍️ Rooroo Documenter (⚡ Cheap/Fast Recommended):** Receives `ROO#` task ID, `context_file_path` (workspace-relative), and `goal`. Creates/updates documentation based on `context.md`. Accesses user project files for in-code documentation using workspace-relative paths. New Rooroo-generated documents (e.g., `rooroo-documenter_api_guide.md`) are stored directly in `.rooroo/tasks/TASK_ID/` (workspace-relative path) or references to modified project docs are reported. Reports back in its strictly parsable JSON Output Envelope with workspace-relative paths (pointing to direct storage in `.rooroo/tasks/TASK_ID/`).
*   **💡 Rooroo Idea Sparker (🧠 Smart/Expensive Recommended):** Can be invoked interactively or tasked by Navigator/Planner. When accessing user project files for context during brainstorming (via `read_file`), it uses workspace-relative paths. Summaries are saved to `.rooroo/brainstorming/` or as task artifacts directly in `.rooroo/tasks/TASK_ID/` (e.g., `.rooroo/tasks/TASK_ID/rooroo-idea-sparker_brainstorm.md`, workspace-relative paths). Reports back via a strictly parsable JSON Output Envelope when tasked, with workspace-relative paths (pointing to direct storage in `.rooroo/tasks/TASK_ID/`).

*Note: The roles of Solution Architect, UX Specialist, and Guardian Validator from previous versions are implicitly covered by the capabilities of the Rooroo Planner to define detailed tasks and the Rooroo Developer/Analyzer/Documenter to execute specialized aspects. If more distinct specialist roles are needed, new Rooroo agents can be defined following the v0.5.0 patterns.*

*Configure the underlying LLM for each agent mode (if supported) to balance cost and capability.*

## 📁 Directory Structure (v0.5.5)

```
<Project Root>/
├── .rooroo/                  # Core rooroo operational directory
│   ├── queue.jsonl           # Pending Tasks (JSON objects, one per line, ROO# IDs, strictly parsable)
│   ├── logs/
│   │   └── activity.jsonl    # Activity Log (JSON objects, one per line, written by Navigator with escaped JSON)
│   ├── tasks/                # Directory for all task-specific data
│   │   ├── ROO#PLAN_20240101120000_initial_project/ # Example Planner Task Directory
│   │   │   └── context.md      # Briefing FOR the Planner for this planning task
│   │   ├── ROO#SUB_initial_project_S001/ # Example Sub-Task Directory (ROO#SUB_... ID from Planner)
│   │   │   ├── context.md      # Task briefing FOR the expert (Created by Rooroo Planner)
│   │   │   ├── feature_component.py # Example artifact directly in task folder
│   │   │   └── data_analysis_report.md # Another example artifact
│   │   ├── ROO#TEMP_20240101130000_fix_login/ # Example Temp Task Directory (ROO#TEMP_... ID from Navigator)
│   │   │   ├── context.md      # Task briefing FOR the expert (Created by Navigator for simple task)
│   │   │   └── login_fix.diff    # Example artifact directly in task folder
│   │   └── ...
│   ├── plans/                # Optional: High-level plan overview documents from Rooroo Planner
│   │   └── ROO#PLAN_20240101120000_initial_project_plan_overview.md
│   └── brainstorming/        # Optional: Summaries from Rooroo Idea Sparker sessions
│       └── brainstorm_summary_ROO#IDEA_20240101140000.md
│
└── src/                      # Example source code directory (Potentially modified by Rooroo Developer)
    └── ...
```

## 📊 Core Data Files (v0.5.5)

### `.rooroo/queue.jsonl`

This file contains the ordered list of tasks to be executed. Each line is a JSON object representing one task. The `rooroo-planner` is responsible for defining the sub-tasks that go here, and the `Rooroo Navigator` consumes it. The Navigator may also add `ROO#TEMP_` tasks for simple, direct actions.

Example task line structure (defined by `rooroo-planner` for sub-tasks in the queue):
```json
{"taskId": "ROO#SUB_parent_S001_setup_database", "parentTaskId": "ROO#PLAN_project_init_123", "suggested_mode": "rooroo-developer", "context_file_path": ".rooroo/tasks/ROO#SUB_parent_S001_setup_database/context.md", "goal_for_expert": "Set up the initial database schema as per specs in context. User spec at 'project_docs/db_schema.md'."}
```
Example task line structure (defined by `Rooroo Navigator` for `ROO#TEMP_` tasks):
```json
{"taskId": "ROO#TEMP_20240101130000_update_readme", "suggested_mode": "rooroo-documenter", "context_file_path": ".rooroo/tasks/ROO#TEMP_20240101130000_update_readme/context.md", "goal_for_expert": "Update the README.md (located at 'README.md') with the latest version number and features."}
```
Key fields per task object:
*   `taskId`: Unique identifier (`ROO#...` format).
*   `parentTaskId`: (For sub-tasks) The ID of the parent planning task.
*   `suggested_mode`: The Rooroo expert agent mode suggested for this task, which the `Rooroo Navigator` uses for delegation (e.g., `rooroo-developer`, `rooroo-analyzer`).
*   `context_file_path`: Workspace-relative path to the Markdown file containing the detailed task briefing (e.g., `.rooroo/tasks/TASK_ID/context.md`). This briefing will use workspace-relative paths when referring to user project files.
*   `goal_for_expert`: A clear, actionable goal. If it involves user project files, paths will be workspace-relative (e.g., `src/main.py`).

### `.rooroo/logs/activity.jsonl`

This append-only file records key events in the workflow. Each line is a JSON object representing one log entry. The `Rooroo Navigator` is primarily responsible for writing to this file using its `SafeLogEvent` internal procedure.

Example log entry structure (simplified, actual structure defined in Navigator's directives):
```json
{"timestamp": "2024-07-25T10:00:05Z", "agent_slug": "rooroo-navigator", "task_id": "ROO#SUB_parent_S001_setup_database", "event_type": "QUEUE_DISPATCH", "severity": "INFO", "details": "Dispatching to rooroo-developer with goal: Set up the initial database schema..."}
{"timestamp": "2024-07-25T10:05:00Z", "agent_slug": "rooroo-developer", "task_id": "ROO#SUB_parent_S001_setup_database", "event_type": "EXPERT_REPORT", "severity": "INFO", "details": "Status: Done. Database schema created.", "output_references": [".rooroo/tasks/ROO#SUB_parent_S001_setup_database/schema.sql"]}
{"timestamp": "2024-07-25T09:55:00Z", "agent_slug": "rooroo-planner", "task_id": "ROO#PLAN_project_init_123", "event_type": "EXPERT_REPORT", "severity": "INFO", "details": "Planning complete. Generated 5 sub-tasks.", "output_references": [".rooroo/plans/ROO#PLAN_project_init_123_plan_overview.md"]}
{"timestamp": "2024-07-25T10:10:00Z", "agent_slug": "rooroo-navigator", "task_id": "ROO#PLAN_project_init_123", "event_type": "SYSTEM_HALT", "severity": "CRITICAL", "details": {"error_code": "QUEUE_WRITE_FAIL", "message": "CRITICAL: Queue update FAILED after task ROO#SUB_another_task! Project state inconsistent."}}
```
Key fields per log entry (will vary by `event_type` as per Navigator's `SafeLogEvent` definition):
*   `timestamp`: ISO 8601 timestamp of the event.
*   `agent_slug`: The Rooroo agent performing or reporting the action.
*   `task_id`: The `ROO#` task ID related to the event.
*   `event_type`: Type of event (e.g., `TRIAGE`, `PLAN_REQUEST`, `QUEUE_DISPATCH`, `EXPERT_REPORT`, `USER_DECISION`, `SYSTEM_HALT`).
*   `severity`: Log level (e.g., `INFO`, `WARN`, `ERROR`, `CRITICAL`).
*   `details`: An object or string containing event-specific information (Navigator ensures JSON is properly escaped if it's structured).
*   `output_references`: Array of workspace-relative paths to relevant artifacts. User project file paths are relative to workspace (e.g., `src/main.py`); Rooroo artifact paths are also relative to workspace and start with `.rooroo/` (e.g., `.rooroo/tasks/TASK_ID/output.json`, pointing directly to the artifact).

### `.rooroo/tasks/TASK_ID/context.md`

For each task (identified by its `ROO#` ID), a Markdown file is created that serves as the comprehensive briefing for the assigned Rooroo expert.
*   For `ROO#SUB_` tasks, this is created by the `rooroo-planner`.
*   For `ROO#TEMP_` tasks, this is created by the `Rooroo Navigator`.
*   For `ROO#PLAN_` tasks, this is created by the `Rooroo Navigator` as the input *for* the `rooroo-planner`.

It includes:
*   Detailed description of the task or planning goal.
*   Specific instructions and requirements.
*   Acceptance criteria.
*   Paths to any input artifacts or relevant existing files (all paths are workspace-relative; user project files e.g., `src/code.py`, Rooroo artifacts e.g., `.rooroo/tasks/PREVIOUS_TASK_ID/data.json`, pointing directly to the artifact).
*   Any other contextual information needed for the agent to perform the work, respecting the unified workspace-relative path system.

The `Rooroo Navigator` passes the workspace-relative path to this `context.md` file to the executing expert or the planner.

### Output Artifacts

- **Primary Output:** Varies by expert (code, documentation, analysis reports, etc.).
- **Storage Location:** All Rooroo-generated artifacts are stored directly within the task's folder: `.rooroo/tasks/TASK_ID/`.
    - For example, if the Developer agent works on task `ROO#DEV123` and produces `output.py`, it will be saved as `.rooroo/tasks/ROO#DEV123/output.py`.
    - If the Analyzer agent works on task `ROO#ANA456` and produces `analysis_report.md`, it will be at `.rooroo/tasks/ROO#ANA456/analysis_report.md`.
    - Specific FILENAMING rules (e.g., prefixing generic names with `agent-slug_` for Developer and Documenter, or ensuring descriptive names for Analyzer) still apply to artifacts placed in this direct location.
- **User Project Files:** Files directly modified or created within the user's project structure (e.g., `src/my_module.py`) are referenced by their workspace-relative paths in reports and logs.

