# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [v0.1.0] - 2025-04-26

### Added

*   **Structured Project Contexts:** Introduced `projectId` to manage distinct project workflows *within* the master state file. Orchestrator now manages an `activeProjectId`.
*   **Dedicated State Directory:** Project state is now managed within the `.state/` directory (`.state/master_project_state.json`).
*   **Dedicated Reports Directory:** Validation reports are now consistently generated in the `.reports/` directory.

### Changed

*   **State Management Overhaul:** Replaced the single, root-level `project_state.json` with a single, structured **master state file** (`.state/master_project_state.json`). This file uses internal `projectId` keys to organize state for multiple distinct projects or tasks, preventing single-file bloat while avoiding the complexity of managing numerous separate state files.
*   **Agent Directive Updates (v4/v5/v6 Hybrid):** All specialist agent instructions updated to:
    *   Receive `projectId` and the master `stateFile` path from the Orchestrator.
    *   Target state reads/edits within their specific `projectId` context inside the master state file (e.g., `projects.<projectId>.tasks.<taskId>.status`).
*   **Validator Reporting Mechanism:** `guardian-validator` now saves reports to `.reports/validation_report_<taskId>_<timestamp>.md` and crucially updates the task log within the master state file (`projects.<projectId>.tasks.<taskId>.log`) with a summary and the **path** to the report file on failure.
*   **Task Delegation Payload:** `master-orchestrator` now includes the `projectId` in the `new_task` payload sent to specialists.

### Fixed

*   **Project Name Persistence:** Resolved the issue where `projectName` wouldn't update; distinct projects are now managed via `projectId` contexts, each with its own `projectName`.
*   **Root Directory Clutter:** Validation reports no longer clutter the root folder.
*   **State File Bloat (Strategy Change):** Mitigated unbounded growth of a single *unstructured* state file by adopting the structured master file approach. (Note: The master file itself can still grow over time).
*   **Inefficiency for Simple Queries:** Addressed the overhead of delegating every minor request by enabling limited Direct Actions for the Orchestrator.

### Removed

*   **Implicit Reliance on Root `project_state.json`:** Agents no longer assume the state file is named `project_state.json` in the root; they use the path provided in task references.


## [v0.0.2] - 2025-04-25

### Changed
- **Agent Directive Overhaul (v2/v3/v4):** Updated agent instructions in `.roomodes` to enhance coordination and robustness.
    - **Centralized State:** Mandated `project_state.json` for all workflow state, task tracking, and status updates.
    - **Standardized Protocols:** Clarified task handoff (Architect defines, Orchestrator delegates based on state), status reporting (agents update `project_state.json`), logging (within `project_state.json`), and `new_task` payload structure.
    - **Refined Error Handling:** Restricted Orchestrator's direct fixes and improved the interactive debugging protocol using `Blocked-Debug` status.
    - **Increased Clarity:** Improved consistency and definitions across agent roles.

## [v0.0.1] - Initial Release
- Initial project setup and definition of core agent roles.