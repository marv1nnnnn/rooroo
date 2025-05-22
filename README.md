# 🚀 rooroo (如如): Minimalist AI Orchestration with Specialist Agents 🚀

**Version: v0.5.9** | [Changelog](changelog.md) | [v0.5.0 Details](v0.5.0.md) | [v0.4.0 Details](v0.4.0.md) | [v0.3.0 Details](v0.3.0.md) | [v0.2.0 Details](v0.2.0.md) | [v0.1.0 Details](v0.1.0.md)

`rooroo` provides **minimalist AI orchestration** for software development using **specialist Rooroo agents** within VS Code via the [Roo Code extension](https://github.com/RooVetGit/Roo-Code). It employs a lean, coordinated team with distinct planning and execution phases, driven by a **`Rooroo Navigator`-led, Output Envelope-based workflow**. Task management relies on `.rooroo/queue.jsonl`, event logging on `.rooroo/logs/activity.jsonl` (now with event severity), detailed task briefings are provided in `.rooroo/tasks/TASK_ID/context.md`, and agent-produced artifacts are stored directly in `.rooroo/tasks/TASK_ID/`. All file paths are relative to the VS Code workspace root, with Rooroo internal files prefixed by `.rooroo/`.

## 🤔 What's in a Name? The Meaning of "rooroo (如如)"

The name "rooroo" comes from the term **"如如" (rú rú)** found in Buddhist philosophy. It relates to the concept of **Tathātā**, often translated as "Thusness" or "Suchness."

![img](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQeUmsB4LIHLErFEbei5g8PfIFG-XQntgqLyA&s)

* **Thusness/Suchness (真如):** Refers to the fundamental, unchanging, true nature of all phenomena – reality as it is.
* **Equality & Non-Duality (不二平等):** Implies that, at the ultimate level, all things share this same essential nature.
* **Emphasis:** The repetition "如如" emphasizes that this inherent "thusness" is pervasive across *all* things.

In the context of this project, the name evokes the idea of an underlying, consistent nature guiding the orchestration. This philosophy informs the **minimalist approach**, focusing on the essential 'thusness' of each agent's specialized role.


## ✨ Key Principles

*   **Minimalism & Specialization:** A small team of specialized Rooroo agents uses appropriate LLM tiers. All file paths are workspace-relative, with Rooroo internal files located under the `.rooroo/` prefix.
*   **Navigator-Led Orchestration:** The **🧭 Rooroo Navigator** acts as the central coordinator and primary user interface. It:
    *   Engages the `rooroo-planner` for complex tasks.
    *   Dispatches tasks from `.rooroo/queue.jsonl`.
    *   Processes structured JSON "Output Envelope" responses from agents (including `error_details`).
    *   Logs events with severity to `.rooroo/logs/activity.jsonl`.
    *   Guides users through decisions and failures.
    *   Uses workspace-relative paths and internal `<thinking>` blocks for complex logic while maintaining concise user communication.
*   **Planner-Managed Queue & Context:** The `rooroo-planner` creates planned tasks for `.rooroo/queue.jsonl` and detailed `context.md` briefings (in `.rooroo/tasks/TASK_ID/`) for each sub-task. It uses workspace-relative paths, assigns appropriate experts, defines actionable goals, and adheres to the **Concise Context File Preparation** guideline.
*   **Output Envelope-Based Communication:** Agents report status, results (including `error_details`), or ask questions by returning a structured JSON "Output Envelope" to the Navigator. All paths within envelopes are workspace-relative, referencing artifacts in `.rooroo/tasks/TASK_ID/`.
*   **Structured Artifacts:** Task-related files (context briefings, agent-produced artifacts) are organized in `.rooroo/tasks/TASK_ID/`, using workspace-relative paths.
*   **Concise Context File Preparation:** When creating `context.md` files, agents prefer Markdown links to large existing files over embedding their full content. Small, critical snippets are acceptable if essential for immediate understanding.
*   **Consistent Task IDs:** All tasks use a unique `ROO#` prefix (e.g., `ROO#PLAN_...`, `ROO#TEMP_...`, `ROO#SUB_...`) for system-wide identification.
*   **Cost-Effectiveness:** Agents leverage appropriate LLM tiers (e.g., Smart vs. Cheap) based on their role and task complexity.
*   **Structured Workflow:** Defined roles, artifacts, communication protocols, and line-oriented data files ensure clarity and robustness.
*   **Principle of Least Assumption:** When faced with ambiguity (regarding user intent, expert selection, file paths, or next steps), agents will **not guess or make assumptions**. Instead, they will explicitly ask for clarification to ensure operations are based on clear understanding.

## 🚀 Get Started & Core Workflow

Follow these steps to use the `rooroo` agent team (v0.5.8+):

1.  **Install Roo Code:** Ensure the [Roo Code VS Code extension](https://marketplace.visualstudio.com/items?itemName=RooVeterinaryInc.roo-cline) is installed.
2.  **Load Modes:** Ensure your `.roomodes` file (v0.5.8 compatible, defining the `rooroo-...` agents, now including a `whenToUse` field for clarity) is in your workspace root. If you roo code version is lower than 3.18.0, you need to use the `.roomodes.json`file and rename it to `.roomodes`.
3.  **Reload VS Code:** Use `Ctrl+Shift+P` or `Cmd+Shift+P` -> "Developer: Reload Window".
4.  **(Optional) Brainstorming:** Use **💡 Rooroo Idea Sparker** for ideation. Summaries can be saved to `.rooroo/brainstorming/` or as task artifacts (`.rooroo/tasks/TASK_ID/rooroo-idea-sparker_summary.md`).
5.  **Activate Navigator:** Select **🧭 Rooroo Navigator** in Roo Code chat.
6.  **State Your Goal:** Describe your project or task to the Navigator.
7.  **Navigator Triage & Planner Engagement:**
    *   The `Rooroo Navigator` analyzes your request, applying the **Principle of Least Assumption** and using workspace-relative paths.
    *   *Complex/Uncertain Tasks:* The Navigator delegates to the **🗓️ Rooroo Planner**.
        *   The Planner creates sub-tasks (`ROO#SUB_...`), prepares `context.md` for each (following **Concise Context File Preparation** by linking to existing files), and assigns an expert (`rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter`).
        *   The Navigator adds these sub-tasks to `.rooroo/queue.jsonl` and informs you.
    *   *Simple, Clear Single-Expert Tasks (for `rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter` only):*
        *   The Navigator may create a `ROO#TEMP_` task for immediate execution or queue a standard task. It prepares `context.md` (concise, linking files).
        *   If the task is ambiguous or for a different expert, the Navigator will ask for clarification or delegate to the Planner.
    *   *Ambiguous Requests:* If your goal or the required expert is unclear, the Navigator will ask for clarification.
8.  **Navigator Executes Plan (Queue Driven & Envelope Driven):**
    *   The `Rooroo Navigator` processes tasks from `.rooroo/queue.jsonl`. It verifies the `suggested_mode` is one of `rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter`.
    *   If the queue is empty, it informs you.
    *   It dispatches the task to the designated expert with `taskId`, `context_file_path` (workspace-relative), and `goal_for_expert`.
    *   The expert agent executes the task, reading its `context.md`. It uses workspace-relative paths for all file access and stores Rooroo-generated artifacts in its task folder: `.rooroo/tasks/TASK_ID/`.
    *   **Crucially:** The expert returns a structured JSON **Output Envelope** to the Navigator upon completion, failure, or if clarification is needed.
9.  **Result Processing & Iteration:**
    *   The `Rooroo Navigator` parses the expert's JSON Output Envelope.
    *   **If `status` is `"NeedsClarification"`:** The Navigator presents the agent's question to you and relays your response back to the expert.
    *   **If `status` is `"Done"` or `"Failed"`:**
        *   The Navigator logs the event to `.rooroo/logs/activity.jsonl`.
        *   It removes the processed task from `.rooroo/queue.jsonl`.
        *   It informs you of the outcome, linking to any artifacts.
        *   If `"Failed"`, the Navigator awaits your decision on how to proceed.
    *   If the queue has more tasks and the previous task was `"Done"`, the Navigator processes the next task from the queue (repeats step 8).
10. **Review Artifacts:** Monitor progress via `.rooroo/queue.jsonl` and `.rooroo/logs/activity.jsonl`. Rooroo-generated artifacts are located in `.rooroo/tasks/TASK_ID/`.

### Workflow Diagram

```text
+----------------------------------------------------------------------+ Phase 0: Optional Brainstorming +----------------------------------------------------------------------+

[User Initiates Brainstorming with 💡 Rooroo Idea Sparker]
(Can be tasked by Navigator/Planner as part of a ROO# task)

+----------------------------------------------------------------------+ Phase 1: User Interaction & Task Triage (Navigator) +----------------------------------------------------------------------+

[User Input (Goal/Request)] -> [🧭 Rooroo Navigator (Applies Principle of Least Assumption)] -> {Triage}
    |
    |--- (Complex/Uncertain/Multi-Expert) --> [Navigator informs User, creates ROO#PLAN_, writes context.md (links preferred), calls Planner]
    |                                |
    |                                v
    |      [Planner returns PlannerOutput (assigns specific expert modes), Navigator adds to queue, informs User]
    |
    |--- (Simple, High-Certainty, Single Expert: Developer/Analyzer/Documenter ONLY) --> [Navigator creates ROO#TEMP_, writes context.md (links preferred), calls Expert Directly] --> [Expert returns Envelope, Navigator processes...]
    |
    |--- (Queue Single Expert: Developer/Analyzer/Documenter ONLY) --> [Navigator creates ROO#..., writes context.md (links preferred), adds to queue, informs User]
    |
    |--- (Ambiguous Goal/Expert) --> [Navigator uses `ask_followup_question` to request clarification from User]
    |
    +--- (User says "Proceed" or task completes successfully and queue is not empty) --> [Navigator proceeds to Phase 2]
    |
    +--- (Queue empty or decision needed) --> [Navigator proceeds to Phase 4]

+----------------------------------------------------------------------+ Phase 2: Queued Task Dispatch (Navigator) +----------------------------------------------------------------------+

[Navigator checks .rooroo/queue.jsonl] --(Not Empty)--> [Navigator Reads Top Task (VERIFIES suggested_mode is dev/analyzer/doc), gets details]
    |                                                                     |
   (Empty)                                                                v
    |                                         [Navigator prepares message (COMMAND: EXECUTE_TASK...), calls Rooroo Expert]
    v                                                                     |
[Navigator informs User queue is empty, moves to Phase 4]                 v
                                                      [Rooroo Expert Executes Task (reads context.md, creates artifacts in .rooroo/tasks/TASK_ID/)]
                                                                          | (Expert returns JSON Output Envelope to Navigator)
                                                                          v
                                          [Navigator Parses Envelope (Proceed to Phase 3)]

+----------------------------------------------------------------------+ Phase 3: Rooroo Expert Report Processing (Navigator) +----------------------------------------------------------------------+

[Navigator receives Rooroo Expert JSON Output Envelope]
    |
    |--- (`status: "NeedsClarification"`) --> [Navigator uses `ask_followup_question` with User] --> [User Response] --> [Navigator relays to Expert for RESUME_TASK] --> (Expert Resumes...)
    |
    |--- (`status: "Done"` or `"Failed"`) --> [Navigator logs to .rooroo/logs/activity.jsonl]
    |                                                  |
    |                                                  +-- [Navigator updates .rooroo/queue.jsonl (removes task)]
    |                                                  |
    |                                                  +-- (Navigator informs User of outcome, links artifacts)
    |                                                  |
    |                                                  +-- (If "Failed") --> [Navigator informs User, moves to Phase 4]
    |                                                  |
    |                                                  +-- (If "Done" & queue has tasks) --> [Loop to Phase 2]
    |                                                  |
    |                                                  +-- (If "Done" & queue empty) --> [Navigator informs User, moves to Phase 4]

+----------------------------------------------------------------------+ Phase 4: User Decision Point (Navigator) +----------------------------------------------------------------------+

[Navigator reaches decision point (plan ready, task failed, queue empty, clarification needed)]
    |
    v
[Navigator uses `ask_followup_question` (applying Principle of Least Assumption if next step unclear) to prompt User]
    |
    v
[Based on User choice, loop to appropriate Phase or await new input]
```

## 🤖 The Agent Team & Cost Optimization

`rooroo` v0.5.8 uses specialized Rooroo agents. All agents operate under their directives, emphasizing the **Principle of Least Assumption** and **Concise Context File Preparation**. Their definitions in `.roomodes` now include a `whenToUse` field for better role clarity. This allows for cost optimization by assigning appropriate LLM tiers.

*   **🧭 Rooroo Navigator (⚡ Cheap/Fast Recommended):** Your primary interface and project guide, embodying principles like "Evidence-Based Operation," "Proactive Logging," "Resilience," and acting as a "Guardian of Protocol." Critically, it adheres to the **Principle of Least Assumption**, asking for clarification instead of guessing. Manages user interaction with a direct, formal style. Triages requests using structured logic: delegates complex or uncertain work to `rooroo-planner`; handles simple, high-certainty tasks *only* for `rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter` directly or via queue (asking for clarification otherwise); asks for clarification on ambiguous goals. Dispatches tasks from `.rooroo/queue.jsonl` (validating `suggested_mode`). Processes agent JSON "Output Envelope" responses. Logs events with severity to `.rooroo/logs/activity.jsonl`. Handles user decisions, especially for failures or ambiguities. Uses internal `<thinking>` blocks for complex logic while maintaining concise user communication. Enforces workspace-relative paths and **Concise Context File Preparation** (preferring links) when creating contexts.
*   **🗓️ Rooroo Planner (🧠 Smart/Expensive Recommended):** Receives directives from the Navigator. As a "Master Strategist," it decomposes complex goals, emphasizing optimal Rooroo expert assignment (`rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter`). Creates detailed context Markdown files for sub-tasks in `.rooroo/tasks/SUB_TASK_ID/context.md`, following the **Concise Context File Preparation** guideline (preferring Markdown links over embedding large content). Reports back via a JSON `PlannerOutput` envelope containing `queue_tasks_json_lines` (with workspace-relative paths).
*   **🧑‍💻 Rooroo Developer (Custom / Varies):** Receives `ROO#` task ID, `context_file_path` (workspace-relative), and `goal` from the Navigator. As an "Engineering Virtuoso," executes coding tasks based on its `context.md`. Accesses user project files and Rooroo artifacts using workspace-relative paths. Rooroo-generated artifacts are stored directly in `.rooroo/tasks/TASK_ID/` or modifies project files. Reports back via a JSON Output Envelope. Adheres to the **Principle of Least Assumption** if context/goal is unclear, asking for clarification.
*   **📊 Rooroo Analyzer (Insightful Investigator) (⚡ Cheap/Fast Recommended):** Receives `ROO#` task ID, `context_file_path` (workspace-relative), and `goal`. As an "Insightful Investigator," performs analysis based on `context.md`, accessing data/code via workspace-relative paths. Generates reports directly in `.rooroo/tasks/TASK_ID/`. Reports back via a JSON Output Envelope. Adheres to the **Principle of Least Assumption**, asking for clarification if goal/context is ambiguous.
*   **✍️ Rooroo Documenter (⚡ Cheap/Fast Recommended):** Receives `ROO#` task ID, `context_file_path` (workspace-relative), and `goal`. As a "Clarity Craftsman," creates/updates documentation based on `context.md`, accessing files via workspace-relative paths. New Rooroo-generated documents are stored directly in `.rooroo/tasks/TASK_ID/`. Reports back via a JSON Output Envelope. Adheres to the **Principle of Least Assumption**, asking for clarification if goal/context is unclear.
*   **💡 Rooroo Idea Sparker (🧠 Smart/Expensive Recommended):** Can be invoked interactively or tasked. As an "Innovation Catalyst," facilitates brainstorming. When accessing user project files for context, uses workspace-relative paths. Summaries saved to `.rooroo/brainstorming/` or as task artifacts in `.rooroo/tasks/TASK_ID/`. Reports back via a JSON Output Envelope when tasked. Adheres to the **Principle of Least Assumption**, asking for clarification if the brainstorming topic is vague.

*Note: The roles of Solution Architect, UX Specialist, and Guardian Validator from previous versions are implicitly covered by the capabilities of the Rooroo Planner to define detailed tasks and the Rooroo Developer/Analyzer/Documenter to execute specialized aspects. If more distinct specialist roles are needed, new Rooroo agents can be defined following the v0.5.0 patterns.*

*Configure the underlying LLM for each agent mode (if supported) to balance cost and capability.*

## 📁 Directory Structure (v0.5.8)

```
<Project Root>/
├── .rooroo/                  # Core rooroo operational directory
│   ├── queue.jsonl           # Pending Tasks (JSON objects, one per line, ROO# IDs, strictly parsable)
│   ├── logs/
│   │   └── activity.jsonl    # Activity Log (JSON objects, one per line, written by Navigator with escaped JSON)
│   ├── tasks/                # Directory for all task-specific data
│   │   ├── ROO#PLAN_20240101120000_initial_project/ # Example Planner Task Directory
│   │   │   └── context.md      # Briefing FOR the Planner (concise, uses links)
│   │   ├── ROO#SUB_initial_project_S001/ # Example Sub-Task Directory (ROO#SUB_... ID from Planner)
│   │   │   ├── context.md      # Task briefing FOR the expert (Created by Planner, concise, uses links)
│   │   │   ├── feature_component.py # Example artifact directly in task folder
│   │   │   └── data_analysis_report.md # Another example artifact
│   │   ├── ROO#TEMP_20240101130000_fix_login/ # Example Temp Task Directory (ROO#TEMP_... ID from Navigator)
│   │   │   ├── context.md      # Task briefing FOR the expert (Created by Navigator, concise, uses links)
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

## 📊 Core Data Files (v0.5.8)

### `.rooroo/queue.jsonl`

This file contains the ordered list of tasks to be executed. Each line is a JSON object representing one task. The `rooroo-planner` defines sub-tasks, and the `Rooroo Navigator` consumes it and may add `ROO#TEMP_` tasks (for `rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter` only).

Example task line structure (defined by `rooroo-planner`):
```json
{"taskId": "ROO#SUB_parent_S001_setup_database", "parentTaskId": "ROO#PLAN_project_init_123", "suggested_mode": "rooroo-developer", "context_file_path": ".rooroo/tasks/ROO#SUB_parent_S001_setup_database/context.md", "goal_for_expert": "Set up the initial database schema as per context. Specs linked in context.md."}
```
Example task line structure (defined by `Rooroo Navigator` for `ROO#TEMP_` task):
```json
{"taskId": "ROO#TEMP_20240101130000_update_readme", "suggested_mode": "rooroo-documenter", "context_file_path": ".rooroo/tasks/ROO#TEMP_20240101130000_update_readme/context.md", "goal_for_expert": "Update the README.md (link in context.md) with latest version."}
```
Key fields per task object:
*   `taskId`: Unique identifier (`ROO#...` format).
*   `parentTaskId`: (For sub-tasks) ID of the parent planning task.
*   `suggested_mode`: The Rooroo expert mode suggested (`rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter`).
*   `context_file_path`: Workspace-relative path to the Markdown briefing (e.g., `.rooroo/tasks/TASK_ID/context.md`).
*   `goal_for_expert`: Clear goal, often referencing links within the `context.md`.

### `.rooroo/logs/activity.jsonl`

Append-only file recording key events with severity. Each line is a JSON object written primarily by the `Rooroo Navigator`.

Example log entry structure (simplified):
```json
{"timestamp": "2024-07-25T10:00:05Z", "agent_slug": "rooroo-navigator", "severity": "INFO", "task_id": "ROO#SUB_parent_S001_setup_database", "event_type": "QUEUE_DISPATCH", "details": {"message": "Dispatching to rooroo-developer..."}}
{"timestamp": "2024-07-25T10:05:00Z", "agent_slug": "rooroo-developer", "severity": "INFO", "task_id": "ROO#SUB_parent_S001_setup_database", "event_type": "EXPERT_REPORT", "details": {"status": "Done", "message": "Database schema created."}, "output_references": [".rooroo/tasks/ROO#SUB_parent_S001_setup_database/schema.sql"]}
{"timestamp": "2024-07-25T09:55:00Z", "agent_slug": "rooroo-planner", "severity": "INFO", "task_id": "ROO#PLAN_project_init_123", "event_type": "EXPERT_REPORT", "details": {"status": "Done", "message": "Planning complete."}, "output_references": [".rooroo/plans/ROO#PLAN_project_init_123_plan_overview.md"]}
{"timestamp": "2024-07-25T10:10:00Z", "agent_slug": "rooroo-navigator", "severity": "ERROR", "task_id": "ROO#SUB_parent_S002_api_integration", "event_type": "EXPERT_REPORT", "details": {"status": "Failed", "message": "API endpoint returned 503.", "error_details": "Connection timed out..."}}
{"timestamp": "2024-07-25T10:15:00Z", "agent_slug": "rooroo-navigator", "severity": "INFO", "task_id": null, "event_type": "USER_CLARIFICATION_REQUEST", "details": {"message": "Asking user for clarification on ambiguous request."}}

```
Key fields per log entry:
*   `timestamp`: ISO 8601 timestamp.
*   `agent_slug`: Agent performing/reporting.
*   `severity`: `INFO`, `WARN`, `ERROR`, `CRITICAL`.
*   `task_id`: Related `ROO#` task ID (can be null).
*   `event_type`: Type of event (e.g., `TRIAGE`, `PLAN_REQUEST`, `QUEUE_DISPATCH`, `EXPERT_REPORT`, `USER_DECISION`, `USER_CLARIFICATION_REQUEST`).
*   `details`: Event-specific info (status, messages, `error_details`).
*   `output_references`: Array of workspace-relative paths to relevant artifacts.

### `.rooroo/tasks/TASK_ID/context.md`

For each task (`ROO#PLAN_`, `ROO#SUB_`, `ROO#TEMP_`), this Markdown file is the comprehensive briefing for the assigned agent. Created by the Planner (for sub-tasks) or Navigator (for planner/temp tasks), following the **Concise Context File Preparation** guideline.

It includes:
*   Detailed description of the goal.
*   Instructions and requirements.
*   Acceptance criteria.
*   **Links** to input artifacts or relevant existing files (all paths workspace-relative, e.g., `[Schema Doc](project_docs/schema.md)`, `[Previous Output](.rooroo/tasks/PREVIOUS_TASK_ID/data.json)`). Small, critical snippets may be embedded if essential.
*   Any other necessary contextual information.

### Output Artifacts

- **Primary Output:** Varies by expert (code, documentation, analysis reports, etc.).
- **Storage Location:** All Rooroo-generated artifacts are stored directly within the task's folder: `.rooroo/tasks/TASK_ID/`.
    - Example: `.rooroo/tasks/ROO#DEV123/output.py`, `.rooroo/tasks/ROO#ANA456/analysis_report.md`.
    - Specific FILENAMING rules may apply (e.g., descriptive names).
- **User Project Files:** Modified/created user files (e.g., `src/my_module.py`) are referenced by workspace-relative paths.

