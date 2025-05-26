## [v0.6.0] - 2025-05-26
### BREAKING CHANGE & Prompting Enhancements

Version 0.6.0 introduces a critical breaking change in the `Rooroo Navigator`'s direct expert invocation protocol and incorporates system-wide prompting enhancements inspired by recent insights into LLM behavior (e.g., "leaked Claude Sonnet prompt" discussions).

- **BREAKING CHANGE: Navigator Direct Expert Invocation (Triage E):**
    - The `Rooroo Navigator` previously included the `--context-file` argument in the command message when directly invoking an expert (Developer, Analyzer, Documenter) for an immediate task (Triage E).
    - In the current `.roomodes`, this `--context-file` argument is **omitted** from the command message specifically for the Triage E scenario: `Delegate: <new_task><mode>{TARGET_EXPERT_MODE}</mode><message>COMMAND: EXECUTE_TASK --task-id {DIRECT_EXEC_TASK_ID} --goal "{refined_goal_for_expert}" ...</message></new_task>`.
    - **Impact:** Expert agents, which rely on the `--context-file` argument to locate their briefing, will fail when invoked via Triage E. This necessitates a correction in the Navigator's Triage E directive to re-include this argument for proper functioning.

- **Inspired Prompting Enhancements (System-Wide):**
    - All Rooroo agent directives in `.roomodes` are being updated to leverage advanced prompting techniques for improved clarity, reliability, and LLM guidance. This includes:
        - **Explicit Role Priming & Persona Definition.**
        - **Structured Directives & Clear Action Steps.**
        - **Emphasis on Critical Principles & Constraints.**
        - **Clear Input/Output Specification with Examples.**
        - **Encouragement of Internal "Thinking" Processes.**
        - **Explicit Handling of Ambiguity** (e.g., Principle of Least Assumption).
    - The goal is to enhance agent performance, reduce misinterpretations, and align agent logic more closely with LLM best practices.

## [v0.5.10] - 2025-05-23
### Agent Logic Enhancements & Protocol Refinements

This version focuses on enhancing the precision and robustness of the `Rooroo Navigator` and refining `Rooroo Planner` capabilities.

- **`Rooroo Navigator` Enhancements:**
    - **Communication Precision:** Further emphasis on extremely concise user-facing messages (typically 1-2 sentences) followed by at most one tool call. Explicitly forbids verbose internal reasoning in final outputs.
    - **Stricter Context File Preparation:** Reinforced the "LINK, DON'T EMBED" rule for `context.md` files. Agents must prioritize linking to existing code, large documents, or complex data, with only very small, critical snippets being permissible if absolutely essential for immediate understanding.
    - **Refined Task Triage (Phase 1):**
        - Planning (Triage D): More explicit triggers (explicit request, multi-expert, high complexity/uncertainty). Planner's "Advice" output is handled more granularly; if actionable for Developer, Analyzer, or Documenter, Navigator can prompt for immediate execution or queuing.
        - Immediate & Queued Single Expert Tasks (Triage E & F): Stricter validation mandates that the `TARGET_EXPERT_MODE` **must** be one of `rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter`. If the correct expert from this list isn't clear, Navigator will ask for clarification (Triage H).
    - **Improved Clarification Handling (Phase 3):** When an expert requests clarification for a queued task, the Navigator now re-adds the task to the front of the queue with a `NeedsClarificationInQueue` status to ensure it's not lost.
    - **Auto-Proceed Logic for Plans:** The condition for auto-proceeding with a plan is now more specific, checking if the next task in the queue shares the same `plan_id` as the completed task.
    - **Robustness in Queue Processing (Phase 2):** Invalid `suggested_mode` in a queued task (i.e., not Developer, Analyzer, or Documenter) is now handled by removing the problematic task and informing the user, rather than halting the system.

- **`Rooroo Planner` Enhancements:**
    - **Ambiguous Expert Assignment:** The Planner can now explicitly flag a sub-task where the choice of expert (`rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter`) is ambiguous by setting `suggested_mode: "AMBIGUOUS_EXPERT_CHOICE"` and providing details for the Navigator to resolve.
    - **Context File Consistency:** Re-emphasized the "LINK, DON'T EMBED" rule for preparing concise sub-task `context.md` files.

- **General:**
    - Continued reinforcement of workspace-relative path conventions and critical path rules for artifact storage across all agents.
    - Minor refinements to other agent directives to align with these principles.

## [v0.5.9] - 2025-05-22
### General Updates

- Converted `.roomodes` to YAML format, which is prefered by Roo code after 3.18.0
- Previous `.roomodes` is now `.roomodes.json`

## [v0.5.8] - 2025-05-16

### Agent Definition Enhancements

- **Added `whenToUse` Field:** Introduced a `whenToUse` field to all Rooroo agent definitions within the `.roomodes` file. This field provides a concise description of when each agent mode is most appropriately used, aiding users in selecting the correct Rooroo expert for their tasks. This change enhances the clarity and usability of the Rooroo agent system.
## [v0.5.6] - 2025-05-13

### Self-Correction & Clarity Enhancements

Version 0.5.6 introduces the **Principle of Least Assumption** to significantly improve self-correction capabilities and reduce errors caused by ambiguity across all Rooroo agents.

- **New Core Principle - Principle of Least Assumption:**
    - When faced with ambiguity regarding user intent, required expert selection, file paths, or next steps, agents will **not guess or make assumptions**.
    - Instead, agents will explicitly ask for clarification, ensuring operations are based on clear understanding rather than potentially incorrect assumptions.
    - This principle applies across all aspects of workflow, from the initial task triage to expert selection and report interpretation.

- **`Rooroo Navigator` Enhancements:**
    - **Improved Ambiguity Handling:** Added explicit triage path for fundamentally ambiguous requests, with clear procedures for requesting clarification.
    - **Stricter Expert Validation:** More rigorous validation of expert mode selection during task triage, preventing delegation to inappropriate experts.
    - **Enhanced Clarification Flow:** Refined the clarification process in Phase 3, with better tracking of pending clarifications and clearer state management.
    - **Concise Context File Preparation:** Introduced a new guideline for preparing `context.md` files, preferring Markdown links to large existing files over embedding their full content to maintain conciseness. This applies to context prepared by the Navigator and expected from the Planner.

- **Expert Selection Safeguards:**
    - Triage paths for immediate and queued tasks now include explicit validation to ensure the target expert is selected from the limited set of allowed experts (`rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter`).
    - If the appropriate expert cannot be determined with high confidence, the system will request clarification rather than proceeding with uncertainty.

## [v0.5.5] - 2025-05-11

### Enhancements & Robustness

Version 0.5.5 focuses on significantly enhancing the `Rooroo Navigator`'s operational robustness, error handling, and directive clarity. It also refines the personas, core principles, and reporting mechanisms for all Rooroo expert agents.

- **`Rooroo Navigator` Major Enhancements:**
    - **Improved Operational Robustness:**
        - **Advanced Logging (`SafeLogEvent`):** Logging now includes event severity levels (`INFO`, `WARN`, `ERROR`, `CRITICAL`) and uses a more robust append-style mechanism for writing to the log file. Error handling for the logging process itself has been improved.
        - **Critical Error Handling Protocol (`HandleCriticalErrorOrHalt`):** A new, explicit protocol for the Navigator to gracefully halt operations in the event of unrecoverable system-level failures (e.g., persistent queue corruption, critical log system failure).
        - **Stricter Error Management in Task Processing:** Enhanced error checking throughout all phases (triage, queue processing, expert report handling). Several conditions that previously might have allowed continued operation with a warning now lead to more decisive actions, including stopping specific flows or invoking the critical halt protocol if project integrity is at risk.
    - **Enhanced Directive Clarity:**
        - More precise rules regarding the conciseness of final user-facing output and stricter definitions of what content is forbidden (e.g., verbose explanations, internal state dumps).
        - Introduction of new core principles such as "Evidence-Based Operation" and "Guardian of the Rooroo Protocol."
        - More detailed procedures for task triage, including clearer handling of ambiguous requests and conditions for automatically proceeding with queued tasks.
        - Refined logic for queue updates, especially ensuring the queue file is correctly emptied if no tasks remain.
    - **Refined Expert Report Processing:** Explicit instructions to process `error_details` if provided in expert reports.

- **Expert Agent Refinements (Planner, Developer, Analyzer, Documenter, Idea Sparker):**
    - **Elaborated Personas & Core Principles:** Each expert agent's role definition, persona, and guiding operational principles have been significantly expanded and clarified, promoting more distinct and sophisticated behaviors.
        - `Rooroo Planner`: Stronger emphasis on assigning specific, appropriate Rooroo expert modes to sub-tasks.
        - `Rooroo Developer`: Enhanced engineering principles, including "Clean Code Philosophy," "Robustness," and "Security Awareness."
        - `Rooroo Analyzer`: More detailed analytical principles like "Hypothesis-Driven Approaches" and "Evidence Supremacy."
        - `Rooroo Documenter`: Expanded documentation principles focusing on "Accuracy," "Navigability," and "Consistency."
        - `Rooroo Idea Sparker`: More structured "Core Facilitation Principles" and refined interaction model.
    - **Improved Expert Reporting:** Directives for `Rooroo Developer`, `Rooroo Analyzer`, and `Rooroo Documenter` now explicitly include the use of an `error_details` field in their JSON reports to the Navigator for conveying technical error information.

## [v0.5.4] - 2025-05-10

### BREAKING CHANGE & Enhancements

Version 0.5.4 introduces a critical simplification in how expert-generated artifacts are stored, moving them directly into the task folder. It also significantly refines the `Rooroo Navigator`'s operational directives, emphasizing concise user-facing communication and detailing its internal processing logic within extensive `<thinking>` blocks. All Rooroo agent custom instructions have been updated to reflect these changes.

- **Simplified Artifact Storage Path (Critical Change):**
    - Expert-generated artifacts are now stored directly in the main task folder: `.rooroo/tasks/TASK_ID/` (e.g., `.rooroo/tasks/ROO#DEV123/output.py`, `.rooroo/tasks/ROO#ANA456/analysis_report.md`).
    - This replaces the previous, more nested structure: `.rooroo/tasks/TASK_ID/artifacts/AGENT_SLUG/`.
    - This change affects how all expert agents store their outputs and how the `Rooroo Navigator` and `Rooroo Planner` refer to and manage these artifacts in task contexts, logs, and user messages.

- **`Rooroo Navigator` Directive Enhancements:**
    - **Conciseness & Thinking Process:** Further emphasis on concise user-facing communication. Detailed internal steps, variable states (e.g., `current_task_json_string`, `task_object`), complex logic (like queue parsing, content generation for files), and the explicit values of variables it is working with are now explicitly mandated to be within extensive `<thinking>` blocks. This ensures the Navigator's operational logic is thoroughly documented internally while keeping user interactions brief.
    - **Path Convention:** Reiteration and reinforcement of the workspace-relative path convention, with Rooroo internal files always prefixed by `.rooroo/`.
    - **Rooroo File System Description:** Updated in Navigator's instructions to reflect the new direct artifact storage within `.rooroo/tasks/TASK_ID/`.
    - **Expert Report Format Example:** The example for `output_artifact_paths` in expert reports now reflects the direct artifact storage path (e.g., `.rooroo/tasks/ROO#TASK_ID/artifact.ext` for Rooroo-generated artifacts, alongside user project file paths like `src/user_file.py`).
    - **Queue Processing (Phase 2):**
        - The internal logic for parsing `.rooroo/queue.jsonl`, determining the current task, and preparing the remaining queue content is now explicitly detailed using pseudo-code within a `<thinking>` block. This includes deriving variables like `current_task_json_string`, `task_object`, `new_queue_content_for_file`, and `num_remaining_tasks_in_queue`.
        - The construction of the `message_for_expert` is also detailed within a `<thinking>` block.
        - User-facing messages are refined for conciseness (e.g., "Processing task: `{task_object.taskId}`. Delegating to `{task_object.suggested_mode}`. {num_remaining_tasks_in_queue} task(s) will remain in queue after this one.").
    - **Expert Report Processing (Phase 3):**
        - Clarified input parameters (`task_id`, `expert_mode`, `expert_report_json`, `new_queue_content_after_removal`, `num_remaining_tasks_in_queue`).
        - User messages listing artifacts now use the simplified, direct paths (e.g., `.rooroo/tasks/TASK_ID/my_artifact.txt`).
        - The `<thinking>` block for queue update details the content being written (`new_queue_content_after_removal`) and the number of tasks for the `line_count` parameter.
        - User-facing messages for task completion are updated to reflect the number of remaining tasks and prompt for "Proceed" if applicable.
    - **Minor Clarifications:** Small updates to `SafeLogEvent` procedure (to include `<thinking>` for log preparation), Phase 1 (Task Triage, e.g., planner's context path, report handling, various `<thinking>` block additions for clarity of internal operations), and Phase 4 (User Decision Point, including more context in the prompt question).

- **Other Agent Directives:**
    - **`Rooroo Planner`:** Instructions updated to ensure sub-task `context.md` files correctly reference any potential input artifacts from previous tasks using the new direct path (`.rooroo/tasks/PREV_SUB_TASK_ID/artifact.ext`) and that `goal_for_expert` implies outputs from the sub-task (if Rooroo artifacts) will also be stored directly in that sub-task's folder (`.rooroo/tasks/SUB_TASK_ID/`). The Planner must now carefully assign the most appropriate Rooroo expert (`rooroo-developer`, `rooroo-analyzer`, or `rooroo-documenter`) for each sub-task's `suggested_mode` field.
    - **`Rooroo Developer`, `Rooroo Analyzer`, `Rooroo Documenter`:** Instructions updated to save primary output artifacts directly into `.rooroo/tasks/TASK_ID/` (e.g., `.rooroo/tasks/TASK_ID/rooroo-developer_output.log`, `.rooroo/tasks/TASK_ID/analysis_report.md`, `.rooroo/tasks/TASK_ID/your_document_name.md`). The specific FILENAMING rule (prefixing generic names with `agent-slug_` for Developer and Documenter, or ensuring descriptive names for Analyzer) still applies to artifacts placed in this now direct location. Each expert is also instructed to provide concise summary messages in their reports to the Navigator, with details placed in the artifacts themselves.
    - **`Rooroo Idea Sparker`:** Instructions updated to reinforce path consistency for reading files (e.g., `.rooroo/tasks/ROO#TASK123/analysis_report.md`) and its standard summary saving location in `.rooroo/brainstorming/`.

## [v0.5.3] - 2025-05-10

### BREAKING CHANGE

Version 0.5.3 introduces a significant simplification and standardization of file path handling across all Rooroo agents. These changes are not backward compatible with v0.5.2. All Rooroo agent custom instructions (Navigator, Planner, Developer, Analyzer, Documenter, Idea Sparker) have been updated to v7.0 to implement these critical changes.

- **Unified Workspace-Relative Path Convention:**
    - **All file paths** (for both user project files and Rooroo internal files) are now **strictly relative to the VS Code workspace root.**
    - The `{{workspace}}` placeholder has been **completely removed** from all agent directives and path examples.
    - **User Project File Paths:** Specified directly from the workspace root (e.g., `src/main.js`, `docs/specification.md`).
    - **Rooroo Internal File Paths:** Also specified from the workspace root, and are always prefixed with `.rooroo/` (e.g., `.rooroo/queue.jsonl`, `.rooroo/tasks/TASK_ID/context.md`).
    - This unified approach simplifies path management and ensures consistency in how agents interpret and use file paths in tool calls, messages, logs, and artifact listings.
- **Agent Directives Updated:** All Rooroo agent custom instructions have been revised to v7.0 to enforce and reflect this new path convention. This includes changes to examples, path constructions in operational logic, and file access descriptions.

## [v0.5.2] - 2025-05-10

### BREAKING CHANGE

Version 0.5.2 introduces critical changes to path handling conventions across all Rooroo agents and refines communication protocols. These changes are not backward compatible with v0.5.1. The custom instructions for all Rooroo agents (Navigator, Planner, Developer, Analyzer, Documenter, Idea Sparker) have been updated to v6.4 to implement these changes.

- **Fundamental Overhaul of Path Handling & Relativity:**
    - A crucial distinction is now made for file path interpretation:
        - **User Project File Paths:** Paths referring to the user's own project files (e.g., source code, documents located in their working directory such as `c:/Users/PC/Documents/code/llm-min.txt`) MUST now be specified and understood by agents as relative to the user's workspace root.
        - **Rooroo Internal File Paths:** Paths referring to Rooroo's operational files (e.g., within `.rooroo/tasks/`, `.rooroo/logs/`, `.rooroo/plans/`) continue to be relative to the Rooroo system's root directory (e.g., `c:/Users/PC/Documents/code/rooroo`).
    - This change affects how all agents (`Rooroo Navigator`, `Rooroo Planner`, `Rooroo Developer`, `Rooroo Analyzer`, `Rooroo Documenter`, `Rooroo Idea Sparker`) handle file paths provided in goals, task contexts (`context.md`), tool parameters (e.g., for `read_file`, `write_to_file`), artifact listings, and Markdown links. All agent directives (v6.4) incorporate this new dual-root path understanding.

- **Stricter JSON Formatting for Agent Communication and Logging:**
    - **Expert Reports:** The JSON string returned by Rooroo experts (Planner, Developer, etc.) to the `Rooroo Navigator` (typically via `attempt_completion`) must now be strictly parsable by `JSON.parse()`.
    - **Navigator Logging (`SafeLogEvent`):** The `Rooroo Navigator`'s internal `SafeLogEvent` procedure for writing to `.rooroo/logs/activity.jsonl` has been updated to require that the JSON log object be stringified and properly escaped before insertion into the log file.

- **Refined `Rooroo Navigator` Persona & Communication Style:**
    - The `Rooroo Navigator`'s communication style is now more direct, formal, and concise, avoiding conversational filler (e.g., "Okay," "Sure," "Let's see"). This aligns with a more focused and professional interaction model.


## [v0.5.1] - 2025-05-09
Hotfix for invalid groups

## [v0.5.0] - 2025-05-09

### BREAKING CHANGE

Version 0.5.0 introduces a fundamental overhaul of the `rooroo` orchestration model, agent communication, and file structure. These changes are not backward compatible with v0.4.x.

- **Coordinator Redesign: `Workflow Coordinator` is now `Rooroo Navigator`**
    - The `workflow-coordinator` mode has been replaced by `rooroo-navigator`.
    - `Rooroo Navigator` operates with a different set of directives, focused on direct agent interaction via "Output Envelopes" rather than monitoring file-based state. It serves as the primary user interface and central coordinator, featuring a distinct persona and interactive communication style.
    - All task IDs are now prefixed with `ROO#` (e.g., `ROO#PLAN_...`, `ROO#SUB_...`, `ROO#TEMP_...`).

- **State Management & File Structure Overhaul: Introduction of `.rooroo/` Namespace**
    - **Retirement of Agent State Files for Reporting:** The primary mechanism for agents to report status or detailed output (`.state/tasks/TASK_ID.json`) has been replaced by "Output Envelopes".
    - **New `.rooroo/` Directory Structure:** All core operational files and task-specific data are now under the `.rooroo/` namespace. The previous `.state/` directory is no longer used for these purposes.
        - Task queue is now `.rooroo/queue.jsonl` (managing `ROO#` tasks).
        - Activity log is now `.rooroo/logs/activity.jsonl` (Navigator uses a `SafeLogEvent` procedure).
        - **Task Context Files:** A dedicated Markdown context file for each task is created at `.rooroo/tasks/TASK_ID/context.md`. This file serves as the primary briefing for the assigned agent (Planner creates for planned tasks, Navigator for temp tasks).
        - **Standardized Artifact Paths:** Agents store their work products in a structured way: `.rooroo/tasks/TASK_ID/artifacts/AGENT_SLUG/`.
        - Optional high-level plans can be stored in `.rooroo/plans/` (created by `rooroo-planner`).
        - Optional brainstorming session notes in `.rooroo/brainstorming/` (created by `rooroo-idea-sparker`).
    - The previous `.state/` subdirectories for general design, docs_proposals, reports, and specs are superseded by the new artifact structure under `.rooroo/tasks/TASK_ID/artifacts/AGENT_SLUG/`.

- **New Agent Communication Protocol: Output Envelopes**
    - Agents (e.g., `rooroo-planner`, `rooroo-developer`) now communicate their results, status, or need for clarification back to the `Rooroo Navigator` by outputting a structured JSON string called an "Output Envelope" (e.g., `{\"status\": \"Done\", \"message\": \"...\", \"output_artifact_paths\": [...]}`). This output is typically provided as the result of an agent's `attempt_completion` call, which the Navigator then processes.
    - This replaces the previous model where agents would write a JSON state file to `.state/tasks/TASK_ID.json` for the coordinator to read and interpret.

- **Modified Agent Responsibilities & Interactions:**
    - **`Rooroo Navigator` (formerly `workflow-coordinator`):** Manages user interaction, triages requests (delegating complex work to `rooroo-planner`, simple tasks directly using `ROO#TEMP_` IDs), dispatches tasks from `.rooroo/queue.jsonl`, processes agent "Output Envelopes", logs events to `.rooroo/logs/activity.jsonl`, and handles user decisions.
    - **`Rooroo Planner` (formerly `strategic-planner`):** Receives directives from the Navigator. Designs project plans, assigns `ROO#SUB_` task IDs. Creates detailed `context.md` files for each sub-task in `.rooroo/tasks/SUB_TASK_ID/context.md`. Reports back to the Navigator via a `PlannerOutput`-style envelope which includes `queue_tasks_json_lines`.
    - **Executing Agents (e.g., `rooroo-developer` formerly `coder-monk`, `rooroo-analyzer`, `rooroo-documenter` formerly `docu-crafter`, `rooroo-idea-sparker` formerly `idea-sparker`):** Receive their task briefing via a `CONTEXT_FILE_PATH` (pointing to the `context.md`). Create artifacts in `.rooroo/tasks/TASK_ID/artifacts/AGENT_SLUG/`. Report back to the Navigator via a standardized JSON Output Envelope.

### Changed
- All agent `customInstructions` in `.roomodes` have been significantly updated to reflect the new `Rooroo Navigator`-centric workflow, the `.rooroo/` file system, "Output Envelope" communication, `ROO#` task IDs, and modified roles. Agent slugs have been updated (e.g., `workflow-coordinator` -> `rooroo-navigator`, `strategic-planner` -> `rooroo-planner`, `coder-monk` -> `rooroo-developer`, etc.).
- Documentation (`README.md`) updated to reflect the new architecture.


## [v0.4.3] - 2025-05-09
### Changed
- Updated `customInstructions` for most agents, reflecting incremental improvements and version changes in their internal directives. Key changes include:
    - **Workflow Coordinator**: Enhanced retry logic by pre-fetching original task details from logs. (Internal version updated)
    - **Strategic Planner**: Stricter logic for `suggested_mode` assignment. Improved error analysis by paying closer attention to `error_message` and structured `error_details` from failed tasks; can now more effectively instruct the Solution Architect to re-plan if Coder Monk fails due to insufficient detail. (Internal version updated)
    - **Solution Architect**: Now described as a "Mandatory Detailer & Decomposer". Emphasizes creating *cohesive functional units* with granular instructions and adding sub-tasks to the *front* of the queue. State file now explicitly mentions optional structured `error_details`. (Internal version updated)
    - **Coder Monk**: Renamed to "Best Effort Executor with Self-Correction". Instructions now include an optional self-correction step using lint/build commands. State file must include `notes` on assumptions/self-correction and can include optional structured `error_details`. (Internal version updated)
    - **UX Specialist, Guardian Validator, DocuCrafter**: State files now explicitly mention optional structured `error_details`. (Internal versions updated)
    - **Idea Sparker**: Minor internal version update in directives.

## [v0.4.2] - 2025-05-09
### Changed

- Updated `customInstructions` across multiple agents, enhancing error handling, path awareness, and clarity:
- **Workflow Coordinator:**
    - **Enhanced Interactive Error Handling for Missing Agent State Files:**
        - When an agent's state file (`.state/tasks/{taskId}.json`) is missing, the Coordinator logs a `missing_state_file_error` to `task_log.jsonl`.
        - It informs the user and interactively prompts for action using `ask_followup_question` with options: `[RetryCheck]`, `[MarkAsFailed]`, `[PlannerInvestigateSystemIssue]`, `[Abort]`.
        - Behavior is conditional on user's choice.
    - **Enhanced Interactive Error Handling for Agent Task Failures:**
        - When an agent reports `status: "Failed"` or `status: "Error"` in its state file (or task is marked as failed due to missing state file), the Coordinator logs `task_failed` to `task_log.jsonl`.
        - It informs the user and interactively prompts for action using `ask_followup_question` with options: `[Retry]`, `[Skip]`, `[PlannerReviewTaskFailure]`, `[Abort]`.
        - Behavior is conditional on user's choice.
    - **Tool Usage Update:** Explicitly mentions `ask_followup_question`.
    - **New `task_log.jsonl` Event Types:** Introduces new event types for interactive error handling (e.g., `missing_state_file_error`, `user_request_recheck_state_file`, `user_mark_as_failed_due_to_missing_state_file`, `user_delegate_system_issue_to_planner`, `user_retry_request`, `user_skip_request`, `user_delegate_to_planner_request`, `user_abort_request`).
- **Strategic Planner:**
    - **System Issue Analysis:** Explicitly instructed to analyze and provide recommendations when invoked by the Coordinator for system-level issues (e.g., an agent failing to produce a state file).
    - **Refined `suggested_mode` Guidance:** More detailed examples for assigning `suggested_mode` based on task `type`.
    - Increased emphasis on "Path-Awareness" and ensuring all file paths are relative to project root.
- **Other Agent Updates (Common Themes: Path-Awareness, Contextual Path Requirements, Output Clarifications):**
    - **Solution Architect:** Title changed to "Path-Aware Design, Sub-task ID & Queue Update". Emphasizes path awareness for inputs and outputs.
    - **Coder Monk:** Title changed to "Path-Aware Code Execution, Debugging & State Output". Now explicitly requires necessary file paths in `CONTEXT_STRING` and will fail if missing.
    - **UX Specialist:** Title changed to "Path-Aware Research, Design, Prototype, Test & State File". Enhanced path awareness and clarifies design artifact storage in `.state/design/` (including linked MD files).
    - **Guardian Validator:** Title changed to "Path-Aware Validation, Evidenced Reporting". Requires more detailed paths in `CONTEXT_STRING` and emphasizes storing detailed reports/evidence in `.state/reports/`.
    - **DocuCrafter:** Title changed to "Path-Aware In-Code & External Documentation Specialist". Clearer instructions for path handling for in-code (`src/`), external (`docs/`), and proposed docs (`.state/docs_proposals/`).
    - **Idea Sparker:** Title changed to "Path-Aware Interactive Brainstorming Partner". Emphasizes path awareness and clarifies primary output is conversation, with optional summaries to `.state/brainstorming/`.
    - General emphasis across these agents on "All paths relative to project root."

## [v0.4.1] - 2025-05-08
### Changed

- Updated `customInstructions` for the `Workflow Coordinator` and `Strategic Planner` agents, incorporating the following key enhancements:
- **Workflow Coordinator:**
    - **Enhanced Reliability in Task Dispatch:**
        - The Coordinator now attempts to delegate a task (via the `new_task` tool) *before* logging the delegation or removing the task from `task_queue.jsonl`.
        - The outcome of the `new_task` tool call is explicitly checked. If delegation fails, a `system_error` is logged to `task_log.jsonl`, the user is informed, and `task_queue.jsonl` remains unchanged (the task stays at the top of the queue). This prevents tasks from being lost due to delegation failures.
        - A task is removed from `task_queue.jsonl` only *after* the `new_task` call succeeds and the `task_delegated` event is logged.
    - **Improved Task Parsing from Queue:** Implemented stricter validation for the `task_id` format when parsing tasks from `task_queue.jsonl`. If a `task_id` is malformed, a `system_error` is logged, and the Coordinator awaits intervention from the `Strategic Planner`.
- **Strategic Planner:**
    - **Refined Task ID Generation:** Provided more specific guidance for the `NNN#type#subject` task ID format. This includes examples for the `type` (e.g., `feat`, `chore`, `docs`, `test`, `fix`, `design`) and the requirement for `subject` to be in `concise_snake_case`. Also clarifies that the `next_nnn_base` for ID numbering should start at `010` if no higher IDs are found in the queue.
    - **Self-Task Integration:** The Planner's initial project planning phase now explicitly includes embedding self-referential tasks (e.g., `NNN#chore#integrate_...`, `NNN#chore#review_...`) with full, correctly formatted IDs.

## [v0.4.0] - 2025-05-08

### BREAKING CHANGE

- **Retirement of `project_overview.json`:** This central JSON file for task and project configuration management has been entirely removed.
- **Introduction of `task_queue.jsonl`:**
    - This new file is now the primary source for task definitions and the execution queue.
    - Each line is a JSON object representing a single task, conforming to a schema including `task_id`, `delegation_details`, `dependencies_original`, `priority`, and `added_to_queue_at`.
    - The `Strategic Planner` is now solely responsible for creating, managing the content and order of, and assigning all final `NNN#` IDs within this file.
    - The `Workflow Coordinator` consumes tasks from the top of this file and rewrites the remainder, but does not otherwise modify its content or structure.
- **Introduction of `task_log.jsonl`:**
    - A new append-only file for structured event logging.
    - Both `Workflow Coordinator` and `Strategic Planner` write timestamped JSON log entries to this file, detailing their actions (e.g., `task_delegated`, `task_completed`, `plan_updated`, `task_integrated_into_queue`) with their respective `actor_mode`.
- **`Workflow Coordinator` Role Overhaul:**
    - Role significantly simplified to be a more mechanical dispatcher and logger.
    - Triage_new user requests to the `Strategic Planner` by creating temporary tasks in `task_queue.jsonl`.
    - Consumes tasks from `task_queue.jsonl`, logs events to `task_log.jsonl`, and delegates to agents.
    - Handles agent completion signals by reading agent state files (`.state/tasks/{taskId}.json`) and logging results to `task_log.jsonl` before proceeding to the next task.
    - Defers all complex planning, queue modification, ID assignment, and error/refinement strategy to the `Strategic Planner`.
- **`Strategic Planner` Role Expansion:**
    - Becomes the sole authority for the content, structure, and integrity of `task_queue.jsonl`.
    - Manages all aspects of task ID generation (final `NNN#` IDs) using a strategy based on existing queue content.
    - Responsible for integrating all new tasks, sub-tasks (from agent proposals), and refinements directly into `task_queue.jsonl`.
    - Handles error analysis and refinement loop initiation by modifying `task_queue.jsonl` (e.g., adding refinement tasks).
    - Logs its planning and queue management activities to `task_log.jsonl`.
- **Retirement of Central `project_configuration`:** The top-level `project_configuration` object, previously in `project_overview.json`, has been removed. Project-specific settings, if needed, are expected to be embedded by the `Strategic Planner` into individual task `delegation_details.context` within `task_queue.jsonl`.

### Changed

- Updated `customInstructions` for `Workflow Coordinator` and `Strategic Planner` to reflect their new roles, responsibilities, and interactions with `task_queue.jsonl` and `task_log.jsonl`.
- Documentation (`README.md`, agent role descriptions) updated to reflect the new architecture and file structures.

## [v0.3.3] - 2025-05-06

### Changed

- **Sub-Task Proposal Structure in Agent State Files:**
    - Agents (`Solution Architect`, `Coder Monk`, `UX Specialist`, etc.) now define `new_tasks_to_integrate` in their `.state/tasks/{taskId}.json` state files using a flattened structure for delegation details. Instead of a nested `delegation_details` object, properties such as `context_for_delegation`, `acceptance_criteria_for_delegation`, and `suggested_mode_for_delegation` are now direct, top-level properties of each sub-task object within the `new_tasks_to_integrate` array.
    - The main `description` field of the sub-task object in the state file is now used for both the task's primary description and its `delegation_details.description` when the task is integrated into `project_overview.json`.
- **Workflow Coordinator Adaptation :**
    - The `Workflow Coordinator` has been updated to parse this new flattened structure for sub-tasks from agent state files.
    - When integrating these sub-tasks into `project_overview.json`, the Coordinator reconstructs the standard nested `delegation_details` object. This ensures the final task structure within `project_overview.json` remains consistent with previous versions.
- **Agent Instruction Updates:** `customInstructions` for `Workflow Coordinator`, `Solution Architect`, `Coder Monk`, `UX Specialist`, `Guardian Validator`, and `DocuCrafter` have been updated to reflect this new flattened format for proposing sub-tasks. This change primarily affects how agents internally define sub-tasks for the Coordinator.

## [v0.3.2] - 2025-05-05

### Added

- **Automated Refinement Loop:**
    - Implemented a mechanism where the `Workflow Coordinator` detects task failures specifically caused by insufficient specification (signaled by `Coder Monk` using a specific error message format).
    - Upon detection, the Coordinator automatically generates a new refinement task (`NNN#chore#refine_{original_subject}`) and delegates it to the `Solution Architect`.
    - The `Solution Architect` is now explicitly instructed to handle these refinement tasks, analyzing the original details and feedback provided in the context, and outputting revised, more detailed `delegation_details` for the original task via a new `task_output` field in its state file.
    - The Coordinator processes the successful refinement task's state file, extracts the `updated_delegation_details` from `task_output`, updates the *original* failed task's details in `project_overview.json`, and resets its status to `Pending` for re-execution.
- **Agent State Schema Update:** Added an optional `task_output` object to the Agent State File schema (`.state/tasks/{taskId}.json`) to facilitate the return of structured data from refinement tasks (specifically `refined_task_id` and `updated_delegation_details`).
- **Project Overview Schema Update:** Added optional `original_task_details` and `feedback` fields within `delegation_details.context` in the `project_overview.json` schema to pass necessary information to refinement tasks.

### Changed

- **Agent Instructions:** Updated `customInstructions` for `Workflow Coordinator`, `Coder Monk`, and `Solution Architect` to implement and support the automated refinement loop.
- **Coder Monk Failure Signaling:** `Coder Monk` (v2.6) is now instructed to explicitly signal failures due to insufficient specifications using `status: "Failed"` and a standardized error message format, triggering the refinement process.
- **Minor Agent Role Updates:** Slightly adjusted titles and descriptions for clarity (e.g., Coder Monk mentions "Refinement Signaling").

## [v0.3.1] - 2025-05-04

### Changed

- **Schema Formalization & Instruction Rigor:**
    - Replaced textual descriptions of schema rules with embedded, formal JSON Schema definitions within the `customInstructions` for both `project_overview.json` (in `Strategic Planner` and `Workflow Coordinator`) and the Agent State File (`.state/tasks/{taskId}.json`) used by all executing agents and processed by the `Workflow Coordinator`.
    - Significantly enhanced the clarity, strictness, and consistency of agent instructions regarding:
        - **Mandatory Schema Adherence:** Explicitly requiring outputs (state files, overview structure) to validate against the defined schemas.
        - **State File Creation:** Emphasizing the creation of the `.state/tasks/{taskId}.json` file as the strict "LAST STEP" for executing agents.
        - **Coordinator Validation:** Highlighting the `Workflow Coordinator`'s responsibility to perform schema validation on received state files before processing.
        - **Sub-task Integration:** Refining the description of how the `Workflow Coordinator` processes `new_tasks_to_integrate`, assigns final IDs, and updates dependencies.
- **Agent Role Definition Refinements:** Minor updates to agent role definitions for improved clarity and alignment with the formalized instructions.


## [v0.3.0] - 2025-05-04

### Added

- **Project-Wide Configuration (`project_configuration`):**
    - `Strategic Planner` can now define an optional, top-level `project_configuration` object in `project_overview.json` to hold project-specific settings (e.g., tool paths, URLs, common env vars).
    - `Workflow Coordinator` now injects this `project_configuration` (if present) into the `delegation_details.context.project_config` of delegated tasks.
    - All executing agents (`Solution Architect`, `UX Specialist`, `Guardian Validator`, `DocuCrafter`) are instructed to check for and use this configuration from their context.
- **Centralized Sub-Task ID Assignment:**
    - Agents defining sub-tasks (like `Solution Architect`) now use a temporary `taskId` format `TEMP#type#subject` in their state file's `new_tasks_to_integrate` array.
    - `Workflow Coordinator` is now responsible for reading these temporary tasks, determining the next sequential `NNN` prefix based on existing tasks in `project_overview.json`, assigning the final `NNN#type#subject` ID, and integrating the tasks with their final IDs into the overview.

### Changed

- **Agent Instructions:** Updated instructions across all relevant agents (`Strategic Planner`, `Workflow Coordinator`, `Solution Architect`, `UX Specialist`, `Guardian Validator`, `DocuCrafter`) to reflect the creation, injection, consumption, or processing of the `project_configuration` object and the handling of temporary (`TEMP#...`) vs. final (`NNN#...`) task IDs.

## [v0.2.2] - 2025-05-01

### Changed

- **Task ID Format:** Updated the `taskId` format separator from colon (`:`) to pipe (`#`). The new format is `NNN#type#subject` (e.g., `010#chore#setup_project`). This change is reflected in agent instructions (Planner, Coordinator, etc.) and documentation.

## [v0.2.1] - 2025-05-01

### Added

- **💡 Idea Sparker (Interactive Partner):** Added a new standalone agent mode designed for collaborative brainstorming and idea exploration. Operates independently of the core workflow.

## [v0.2.0] - 2025-04-30

### Changed

- **Orchestration Model & Triage Overhauled (Breaking Change & Optimization):** Control flow inverted.
    - `Workflow Coordinator` (Cheap Model) is now the **Primary Orchestrator & Interface**. Performs strict, rule-based triage on initial requests.
    - `Strategic Planner` (Smart Model) is now an **On-Demand Specialist**, invoked by the Coordinator for complex planning. Creates/updates `project_overview.json`.
- **Workflow Coordinator Logic (Breaking Change & Optimization):**
    - **Triage:** Implements a strict, rule-based decision tree for initial requests, delegating to Planner (Smart), Architect (Smart), `coder-monk`, or DocuCrafter (Cheap) based on keywords/complexity. Asks user if ambiguous.
    - **Delegation Mechanism (Execution):** Relies **strictly** on the `suggested_mode` field (provided by the Planner) within `project_overview.json` task `delegation_details` for delegating execution tasks to the appropriate agent (e.g., `coder-monk`, `solution-architect`).
    - **State Management (Decoupled & Signal Driven):**
        - Waits for platform signals indicating task completion.
        - **Reads** the completed task's state file (`.state/tasks/{taskId}.json`, created by the executing agent) to get final status, outputs, planned subtasks, validation results, etc.
        - Validates the structure of the read state file.
        - Updates `project_overview.json` using **batch operations** based on results read from task state files.
    - **Efficiency:** Designated as a **Cheap Model** role.
- **Strategic Planner Role & Output (Breaking Change):**
    - Invoked via `<new_task>` by the Coordinator.
    - Primary output is `project_overview.json`, including `delegation_details` with a **`suggested_mode`** field for the Coordinator.
    - Uses strict `NNN:type:subject` format for `taskId`.
    - **Does not** create individual `.state/tasks/{taskId}.json` files.
- **Agent State Reporting (Breaking Change - Decoupled State):**
    - All executing agents (`solution-architect`, `ux-specialist`, `guardian-validator`, `docu-crafter`, `coder-monk`) now report completion status, outputs, logs, etc., by **creating/writing their own task state file** (`.state/tasks/{their_taskId}.json`) as their final step.
    - Agents **DO NOT** modify `project_overview.json` directly.
    - `solution-architect` output includes `planned_subtasks` in its state file.
    - `guardian-validator` output includes `validation_result_for_target` (with `target_task_id`) in its state file.
- **Model Tier Designation:** Explicitly added "(Smart Model)" or "(Cheap Model)" designations to agent roles.
- **Artifact Storage Location:** Consolidated outputs (specs, designs, docs, reports) into subdirectories within `.state/` (e.g., `.state/specs/`).
- **Task ID Format:** Enforced strict `NNN:type:subject` format for `taskId` created by the Planner.

### Added

- **`suggested_mode` Field:** New required field in `project_overview.json` task `delegation_details`, populated by Planner, used by Coordinator for delegation.
- **Explicit Triage Logic:** Added rule-based triage to Coordinator.
- **`coder-monk`:** New custom agent mode responsible for executing coding/refactoring/debugging tasks and reporting status via its own state file. Replaces direct delegation to built-in modes for these task types in the Coordinator's primary flow.
- **State File Validation:** Coordinator now has explicit steps to validate the structure of `.state/tasks/{taskId}.json` files upon reading them.

### Removed

- **Direct Overview Updates by Specialists:** Removed capability/instruction for specialist agents to directly update `project_overview.json`.
- **Ambiguous Coordinator Delegation Logic:** Replaced with strict adherence to Planner's `suggested_mode`.
- **Coordinator's Explicit User Confirmation Loop for Built-ins:** The explicit `<ask_followup_question>` loop after built-in task completion (from v0.1.2) is removed. Completion is now signaled by the `coder-monk` creating its state file.
- **Planner's `<switch_mode>` Handoff:** Removed.
- **Direct Delegation to Built-in `code`/`debug` by Coordinator:** Coordinator now delegates coding tasks to the specific `coder-monk` based on Planner's `suggested_mode`.

## [v0.1.2] - 2025-04-28
### Changed
- **Workflow Coordinator Dispatch Logic Overhauled (Hybrid Model):** The `workflow-coordinator` (now v5 implicitly) uses a fundamentally different dispatch mechanism:
    - **Type-Based Mapping:** Now uses a fixed mapping (defined in its instructions) to determine the target mode based on the task `type`.
    - **Hybrid Delegation:**
        - Specialist Tasks (`tech-design`, `ui-design`, `ux-design`, `validation`, `test-execution`, `documentation-*`): Delegated to the corresponding custom specialist modes (`solution-architect`, `ux-specialist`, `guardian-validator`, `docu-crafter`) using `<new_task>` and passing the `taskStateFile`. Relies on these specialists to update the overview status directly upon completion/error.
        - Coding/Fixing Tasks (`feature`, `refactor`, `chore`, `bugfix`): Explicitly delegated to the built-in `code` and `debug` modes using `<new_task>` **without** passing the `taskStateFile`.
    - **Completion Handling for Built-in Modes:** Shifted from primary reliance on inference to **explicitly requiring user confirmation** via `<ask_followup_question>` before updating the status of tasks handled by built-in modes to `Implemented`.
- **Strategic Planner Task Definition:**
    - Now assigns tasks using a **predefined, fixed list of `type` values**.
    - No longer defines or plans an `intendedMode`; the `type` alone dictates the Coordinator's dispatch action.
- **Specialist Agent Instructions Refined:** Instructions for `solution-architect`, `ux-specialist`, `guardian-validator`, and `docu-crafter` updated to:
    - Explicitly reference the specific `type`(s) of tasks they handle.
    - Reinforce their responsibility to directly update **only** the `project_overview.json` status for their assigned task upon completion ('Done', 'Validated', 'Failed') or error ('Error', 'Blocked-Debug').
- **State Schema (`project_overview.json`):**
    - The list of allowed values for the `type` field is now explicitly defined and fixed within the Planner/Coordinator instructions.
    - The `Implemented` status remains necessary for the multi-step flow involving built-in modes and user confirmation/testing.

### Removed
- **Coordinator Logic:** Removed the primary reliance on *inferring* completion status for tasks delegated to built-in modes. Replaced with an explicit user confirmation loop.


## [v0.1.1] - 2025-04-28

### Changed
- **Workflow Coordinator Delegation Refined:** Updated `customInstructions` for `workflow-coordinator` (v3 implicitly) to refine task delegation logic:
    - `tech-design` tasks are now directed to `solution-architect`.
    - `ui-design` and `ux-design` tasks are now directed to `ux-specialist`.
    - Both `test_execution` and `validation` tasks are now explicitly directed to `guardian-validator`.

## [v0.0.1] - Initial Release
- Initial project setup and definition of core agent roles.

## [v0.5.7] - 2025-05-15

### Role Definition Refinements

Version 0.5.7 refines the agent role definitions to be more concise and focused on function rather than specific individuals as inspiration:

- **Enhanced Role Clarity:** Agent role definitions have been refined to focus on capabilities and functions rather than specific named references.
- **More Concise Descriptions:** The role definitions now provide clearer, more straightforward explanations of each agent's purpose and specialization.
- **Professional Tone:** The language across all agent definitions has been standardized for a more consistent and professional tone.
- **Function Over Inspiration:** Definitions now emphasize what each agent does rather than who or what inspired its approach.

