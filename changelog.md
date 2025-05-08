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
    - All executing agents (`Solution Architect`, `UX Specialist`, `Guardian Validator`, `DocuCrafter`, `Coder Monk`) are instructed to check for and use this configuration from their context.
- **Centralized Sub-Task ID Assignment:**
    - Agents defining sub-tasks (like `Solution Architect`) now use a temporary `taskId` format `TEMP#type#subject` in their state file's `new_tasks_to_integrate` array.
    - `Workflow Coordinator` is now responsible for reading these temporary tasks, determining the next sequential `NNN` prefix based on existing tasks in `project_overview.json`, assigning the final `NNN#type#subject` ID, and integrating the tasks with their final IDs into the overview.

### Changed

- **Agent Instructions:** Updated instructions across all relevant agents (`Strategic Planner`, `Workflow Coordinator`, `Solution Architect`, `UX Specialist`, `Guardian Validator`, `DocuCrafter`, `Coder Monk`) to reflect the creation, injection, consumption, or processing of the `project_configuration` object and the handling of temporary (`TEMP#...`) vs. final (`NNN#...`) task IDs.

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

## [v0.1.0] - 2025-04-28

### BREAKING CHANGE
- **Major Architectural Shift:** Replaced the single `master-orchestrator` agent with a two-part system (`strategic-planner` and `workflow-coordinator`) to separate planning and execution management.
    - **`strategic-planner`:** Now handles initial goal interpretation, high-level planning, and initial state file setup (`project_overview.json`, `.state/tasks/`). It then hands off execution management.
    - **`workflow-coordinator`:** Now manages the execution phase, monitoring `project_overview.json`, delegating tasks, interpreting outcomes, handling user test decisions, and managing errors.
- **Delegation Model Changed:** The `workflow-coordinator` now delegates implementation/fixing tasks (`feature`, `bugfix`, `refactor`, `chore`) primarily to the **built-in `code` and `debug` modes** instead of the previous dedicated `apex-implementer` agent. Specialist tasks (design, validation, docs) are still delegated to specialist agents.
- **Removed Agent:** Removed the `apex-implementer` specialist agent. Its responsibilities are now handled by the built-in `code` and `debug` modes under the direction of the `workflow-coordinator`.
- **State Management for Built-in Modes:** The `workflow-coordinator` is now responsible for inferring the completion status of tasks delegated to built-in modes (which cannot update state files directly) and updating `project_overview.json` accordingly.

### Added
- **New Agent:** `strategic-planner` - Focuses solely on initial planning and state setup.
- **New Agent:** `workflow-coordinator` - Focuses solely on execution management, task delegation (including to built-in modes), and state updates during the workflow.
- **Handoff Mechanism:** Introduced an explicit handoff where `strategic-planner` uses `<switch_mode>` to pass control to `workflow-coordinator`.

### Changed
- **Error Handling:** Protocols adapted to handle errors originating from both specialist agents (diagnosed via their state files) and built-in modes (often requiring more user interaction or diagnostics via `debug` mode).
- **Test Execution Flow:** The `workflow-coordinator` now manages the user decision point for test execution after implementation tasks (handled by `code`/`debug`) are inferred to be complete.
- **Specialist Agent Interaction:** `solution-architect`, `ux-specialist`, `guardian-validator`, `docu-crafter` retain their core roles but now interact with the `workflow-coordinator`.

### Removed
- **Agent:** `master-orchestrator` - Responsibilities split between the new `strategic-planner` and `workflow-coordinator`.
- **Agent:** `apex-implementer` - Responsibilities transferred to built-in `code`/`debug` modes managed by the `workflow-coordinator`.

## [v0.0.7] - 2025-04-27

### Changed
- **Agent Directives Updated (Split State):** Updated `customInstructions` across multiple agents to implement a split state management strategy using `project_overview.json` for high-level status and `.state/tasks/{taskId}.json` for detailed task information. If previsouly work with `project_state.json`, we suggest deleting it and using the new `project_overview.json` and `.state/tasks/{taskId}.json` files.
- **State Management Strategy:** Refined the "CRITICAL JSON EDITING STRATEGY" to apply specifically to either the own task file or the overview file depending on the agent's role and the information being updated.
- **Core Optimization Strategy:** Implemented a core strategy for agents to optimize state I/O. This strategy prioritizes the task payload, reads the agent's own task file only once initially, performs selective external reads (overview, docs, specs, design) only when necessary, consolidates multiple updates to the agent's own task file into a single edit operation per logical step or completion, and minimizes updates to the project overview file by only updating the summary status for the agent's task ID upon reaching a final state (Done, Implemented, Error, Failed, Validated).

## [v0.0.6] - 2025-04-26

### Fixed
- **Apex Implementer Behavior:** Fixed an issue where the Apex Implementer mode would repeatedly read files unnecessarily.
- **Safe JSON Editing Strategy:** Added explicit "CRITICAL JSON EDITING STRATEGY" instructions to all agents (`Master Orchestrator`, `Solution Architect`, `UX Specialist`, `Apex Implementer`, `Guardian Validator`, `DocuCrafter`) that modify `project_state.json`. This strategy mandates reading the relevant object, reconstructing the full object with changes, mentally validating syntax, and replacing the entire object via `edit` to minimize syntax errors caused by incremental patching. Includes error handling instructions if edits fail.

### Changed
- **Agent Directives Updated (v5/v6/v7/v8/v9/v10):** Updated `customInstructions` across multiple agents to incorporate the Safe JSON Editing Strategy alongside previous context-aware and workflow changes.

## [v0.0.5] - 2025-04-26

### Added
- **New Task Status:** Added `'Implemented'` status to `project_state.json` task schema to signify code/unit tests written but tests not yet executed, creating a user decision point.

### Changed
- **Workflow Modification:** Introduced an explicit user decision point managed by the Master Orchestrator after a task reaches the `'Implemented'` status.
- **Master Orchestrator Workflow:** Orchestrator now detects `'Implemented'` status, **prompts the user** to decide on test execution (run tests, skip to validation, defer), and delegates the next step (a specific test execution task or a validation task) based on user input. Updated `customInstructions` to v6.
- **Apex Implementer Behavior:** Implementer now writes unit tests but **stops before executing them**, updating task status to `'Implemented'` upon successful code/test writing. Updated `customInstructions` to v8.
- **Test Execution Delegation:** Orchestrator now delegates test execution (when requested by user) as a distinct task (e.g., `type: 'chore'`), often targeting `Guardian Validator` or potentially `Apex Implementer`.
- **Guardian Validator Capability:** Validator instructions updated (v6) to handle specific "run tests" tasks in addition to full validation tasks, reporting pass/fail accordingly.

## [v0.0.4] - 2025-04-26

### Added
- **Task `type` Field:** Introduced a mandatory `type` field (`feature`, `bugfix`, `refactor`, `chore`, `init`, `design`, `validation`, `documentation`) to the `project_state.json` task schema to enable more adaptive workflows.
- **Enhanced Task References:** Added optional `references.reports` and `references.sourceCode` to the task schema.
- **Acceptance Criteria:** Added optional `acceptanceCriteria` array to the task schema.

### Changed
- **Agent Directives Updated (v4/v6):** Updated `customInstructions` for Master Orchestrator (v4), Solution Architect (v4), Apex Implementer (v6), and Guardian Validator (v4) to recognize and adapt behavior based on the new task `type`.
- **Adaptive Orchestration:** Master Orchestrator now delegates tasks based on their `type`, enabling different workflows for features, bug fixes, refactoring, etc.
- **Adaptive Design/Analysis:** Solution Architect now handles analysis for bug fixes/refactors and creates type-specific plans/specs.
- **Adaptive Implementation:** Apex Implementer now adjusts its process based on task type (reading specs, bug reports, or refactoring plans) and emphasizes type-specific testing.
- **Adaptive Validation:** Guardian Validator now tailors its validation strategy based on task type (checking feature criteria, confirming bug fixes, ensuring refactor regressions).
- **Task Status Clarification:** Added `Validated` and `Failed` to the list of possible task statuses in schema documentation for clarity.

## [v0.0.3] - 2025-04-26

### Changed
- **Agent Directives Updated (v3/v4/v5):** Enhanced custom instructions across all agents for better coordination, stricter state management, and refined error handling centered around `project_state.json`.
- **Stricter Schema Enforcement:** Explicitly defined and mandated adherence to the `project_state.json` schema structure for all state interactions.
- **Master Orchestrator:** Added schema reference, enabled concurrent implementation task delegation, and improved reactive error/debug monitoring.
- **Apex Implementer:** Made post-implementation code refinement optional/opportunistic and added structured error details to the state log on failure.
- **Guardian Validator:** **Shifted detailed failure reports** from the state file log to dedicated files within a new `.reports/` directory, linking them from the state log.
- **General:** Minor clarifications on state interaction (`read`/`edit`), task references, and debugging protocols across agents.

## [v0.0.2] - 2025-04-25

### Changed
- **Agent Directive Overhaul (v2/v3/v4):** Updated agent instructions in `.roomodes` to enhance coordination and robustness.
- **Centralized State:** Mandated `project_state.json` for all workflow state, task tracking, and status updates.
- **Standardized Protocols:** Clarified task handoff (Architect defines, Orchestrator delegates based on state), status reporting (agents update `project_state.json`), logging (within `project_state.json`), and `new_task` payload structure.
- **Refined Error Handling:** Restricted Orchestrator's direct fixes and improved the interactive debugging protocol using `Blocked-Debug` status.
- **Increased Clarity:** Improved consistency and definitions across agent roles.

## [v0.0.1] - Initial Release
- Initial project setup and definition of core agent roles.