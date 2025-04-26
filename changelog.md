# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [v0.0.6] - 2025-04-26

### Fixed
- **Apex Implementer Behavior:** Fixed an issue where the Apex Implementer mode would repeatedly read files unnecessarily.

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