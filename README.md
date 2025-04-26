# 🚀 rooroo (如如): Minimalist AI Orchestration with Swiss Army Knife Agents 🚀

**Version: v0.1.0**

Welcome to `rooroo`, an AI-powered system designed to achieve **minimalist AI orchestration** for software development using a focused crew of **'Swiss Army Knife' agents** right within your VS Code environment via the [Roo Code extension](https://github.com/RooVetGit/Roo-Code). Think of it as having a lean, expert virtual team, precisely coordinated.

## 🤔 What's in a Name? The Meaning of "rooroo (如如)"

The name "rooroo" comes from the term **"如如" (rú rú)** found in Buddhist philosophy. It relates to the concept of **Tathātā**, often translated as "Thusness" or "Suchness."

![img](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQeUmsB4LIHLErFEbei5g8PfIFG-XQntgqLyA&s)

*   **Thusness/Suchness (真如):** Refers to the fundamental, unchanging, true nature of all phenomena – reality as it is.
*   **Equality & Non-Duality (不二平等):** Implies that, at the ultimate level, all things share this same essential nature.
*   **Emphasis:** The repetition "如如" emphasizes that this inherent "thusness" is pervasive across *all* things.

In the context of this project, the name evokes the idea of an underlying, consistent nature guiding the orchestration. This philosophy informs the **minimalist approach**, focusing on the essential 'thusness' of each agent's specialized role.

## 🎯 Key Problems Addressed

`rooroo` is designed to tackle common challenges in leveraging AI for software development:

1.  **Over-Complexity & Lack of Focus:** Many AI systems try to be everything, leading to complex, inefficient workflows. `rooroo` addresses this with a **minimalist crew of highly specialized agents**. Each agent acts like a focused **"Swiss Army Knife" component**, excelling in its specific domain (design, coding, validation, documentation), ensuring depth of expertise without unnecessary bloat.
2.  **Interaction Overhead & Delegation Bottlenecks:** Managing multiple AI interactions can be cumbersome. `rooroo` solves this by having a **single point of contact: the Master Orchestrator**. This central coordinator handles task delegation and workflow management. Crucially, it can also resolve simple issues directly, reducing the overhead of delegating every minor task and preventing bottlenecks in the **minimalist orchestration**.
3.  **Inconsistent Development Practices:** AI-driven development can sometimes lack structure. `rooroo` promotes **Document-Driven Development (DDD) and Test-Driven Development (TDD)** principles. It enforces a structured workflow using specifications (`.specs/`, `.design/`) and validation, and includes a dedicated **DocuCrafter** agent (another 'Swiss Army Knife') to manage project documentation (`.docs/`), ensuring clarity and reliability.

## 🤔 Initial Design Philosophy

The `rooroo` project was conceived with several core principles in mind, directly addressing the problems above:

*   **Simplicity & Minimalism:** Avoid unnecessary complexity. The agent team is kept to a focused minimum, with clear, distinct roles (Addresses Problem 1).
*   **Specialized Components (Swiss Army Knives):** Each agent is designed like a "Swiss Army Knife" component, highly capable within its specific domain (Addresses Problem 1).
*   **Centralized Orchestration:** A single Master Orchestrator manages the workflow and communication, capable of handling simple tasks directly (Addresses Problem 2).
*   **Document & Test-Based Approach:** Emphasize clarity and reliability through a workflow encouraging DDD/TDD, supported by dedicated agents (Addresses Problem 3).

## ✨ Why Use rooroo? ✨

`rooroo` offers a structured and efficient approach to AI-assisted development:

*   **🎯 Focused Expertise (Swiss Army Knife Agents):** Delegate tasks to the right AI expert. Instead of one generalist AI, `rooroo` uses highly specialized agents, leading to potentially higher quality results in each domain. (Solves Problem 1)
*   **⚙️ Simplified Orchestration:** You interact primarily with the **🧠 Master Orchestrator**. It handles the complexity of breaking down goals, delegating tasks, managing the workflow, and resolving minor issues, keeping your interaction focused and reducing overhead. (Solves Problem 2)
*   **🏗️ Structured Workflow:** `rooroo` follows a defined process (Design, Implement, Validate), bringing clarity and predictability, encouraging DDD/TDD practices. (Solves Problem 3)
*   **💾 Clear Artifacts:** Key outputs are organized (`.specs/`, `.design/`, `.docs/`), creating a traceable project history and supporting the DDD/TDD approach. (Supports Problem 3)
*   **🎯 Focused Execution:** Each 'Swiss Army Knife' agent works on specific, delegated tasks based on clear inputs, reducing ambiguity and improving reliability. (Supports Problem 1 & 3)

## 🔑 Core Concepts

Understanding these ideas is key to leveraging `rooroo`:

1.  **Minimalist Agent Crew:** `rooroo` operates with a lean team of distinct AI agents (modes). Each agent is a focused 'Swiss Army Knife', optimized for a specific role.
2.  **Orchestration & State Management:** The **🧠 Master Orchestrator** mode is central. It interprets goals, plans strategy, delegates tasks to the 'Swiss Army Knife' agents (passing `projectId`), monitors progress via the master state file (`.state/master_project_state.json`), handles *simple direct actions* itself, and synthesizes results. It manages the overall **minimalist orchestration** process using a central state file containing distinct project contexts.
3.  **Swiss Army Knife Roles:** Each agent (Architect, UX Specialist, Implementer, Validator, DocuCrafter) has a clearly defined, specialized responsibility and operates precisely within that scope.
4.  **Structured Artifacts:** The system relies on artifacts (`.specs/`, `.design/`, `.docs/`, `.reports/`) to maintain context and provide clear inputs/outputs between agents, facilitating DDD.
5.  **Centralized Master State (`.state/master_project_state.json`):** A single JSON file acts as the source of truth for workflow status, task definitions, dependencies, and logs across multiple projects (identified by `projectId`), enabling robust coordination between agents within their assigned context.
## 🔄 The Core Development Workflow

`rooroo` guides feature development through a structured, **minimalist** lifecycle managed by the Orchestrator:

1.  **🎯 Goal Setting:** You provide your high-level goal to the **🧠 Master Orchestrator**.
2.  **✍️ Planning & Design:** The Orchestrator plans phases and delegates detailed design tasks to the relevant 'Swiss Army Knife' agents:
    *   **📐 Solution Architect:** Creates technical specifications (`.specs/`).
    *   **🎨 UX Specialist:** Defines user experience and UI design (`.design/`).
3.  **💻 Implementation:** Once designs are ready, the Orchestrator assigns precise coding tasks to the **⚡ Apex Implementer**, referencing the specs.
4.  **✅ Validation:** The **🛡️ Guardian Validator** independently verifies the implemented features against the specifications (TDD aspect), generating reports in `.reports/`.
5.  **🔄 Iteration:** Based on validation results, the Orchestrator manages feedback loops, assigning refinements or fixes back to the appropriate agents.

*(Note: Documentation tasks are handled separately by the DocuCrafter, see below).*

## 🤖 Meet the Crew (The Swiss Army Knives) 🤖

*   **🧠 Master Orchestrator (Balanced Coordinator):** Top-level AI coordinator. Manages project contexts within `.state/master_project_state.json`. Interprets goals, handles *direct read-only info requests* (state, specific files), plans phases, delegates specialized tasks (passing `projectId`). Monitors progress, handles limited errors, triggers debugging, integrates results, communicates. (Key to solving Problem 2)
*   **📐 Solution Architect (Blueprint Creator):** Expert AI technical designer. Analyzes requirements, researches, creates blueprints (`.specs/`), defines implementation tasks within the specified project context in the master state file. Requests debugging when stuck. (Key to solving Problem 1 & 3)
*   **🎨 UX Specialist (User Advocate):** Expert AI UX/UI designer. Defines user flows, UI structures, accessibility. Outputs specs to `.design/`. Updates status within the specified project context in the master state file. (Key to solving Problem 1 & 3)
*   **⚡ Apex Implementer (Precision Builder):** Elite AI coder. Executes tasks based on specs (`.specs/`, `.design/`), context (`.docs/`), using the specified project context in the master state file. Writes tested code, requests debugging, performs refinement. Updates status in the project state file. (Key to solving Problem 1 & 3)
*   **🛡️ Guardian Validator (Independent Verifier):** Objective AI QA agent. Validates features against specs (`.specs/`, `.design/`), context (`.docs/`), using the specified project context in the master state file. Creates report in `.reports/`, updates status and links report in the project state file. (Key to solving Problem 1 & 3)
*   **✍️ DocuCrafter (Markdown Documentation Generator):** AI specialist for generating/updating Markdown documentation in `.docs/`. Acts on tasks, updating status within the specified project context in the master state file. (Key to solving Problem 3)

## 🚀 Get Started! 🚀

To use this specific `rooroo` agent team, you need the [Roo Code VS Code extension](https://marketplace.visualstudio.com/items?itemName=RooVeterinaryInc.roo-cline) installed.

Once Roo Code is installed:

1.  **Override Local Modes:** Copy the `.roomodes` file from this repository into the root directory of your VS Code workspace. This file defines the `rooroo` **minimalist crew**.
2.  **Reload Roo Code:** Reload the VS Code window (`Ctrl+Shift+P` or `Cmd+Shift+P` -> "Developer: Reload Window").
3.  **Activate the Orchestrator:** Open a Roo Code chat and select the **🧠 Master Orchestrator** mode.
4.  **State Your Goal:** Clearly describe the project or task.
5.  **Collaborate:** Follow the Orchestrator's lead as it manages the **minimalist orchestration** process.
6.  **Manage Documentation:** Explicitly ask the Orchestrator to delegate `init` or `update` tasks to the **✍️ DocuCrafter**.
7.  **Review Artifacts:** Monitor progress by reviewing the outputs from each 'Swiss Army Knife' agent in their respective directories (`.specs/`, `.design/`, `.docs/`, `.reports/`) and checking the master state file (`.state/master_project_state.json`).

Let `rooroo` bring **minimalist orchestration** and **specialized expertise** to your AI development!