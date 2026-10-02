# Agent team

The Mona's Project Pulse dashboard will be built by a custom team orchestrated with GitHub Copilot CLI in a Codespace:

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 (copilot) | Coordinates the specialist agents, breaks the dashboard work into phases, assigns non-overlapping file scopes, manages dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository, documentation, dependencies, edge cases, risks, and validation needs, then produces the implementation plan for the Orchestrator. | `.github/agents/planner.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements the application logic and runnable Project Pulse support, including assigned code and launch configuration, with explicit errors and testable behavior. | `.github/agents/coder.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Defines and implements the dashboard's UI/UX, accessibility, information hierarchy, responsive behavior, visual styling, project cards, status badges, and priority treatment. | `.github/agents/designer.agent.md` |

The Orchestrator starts with the Planner, then delegates implementation and design work to the Coder and Designer according to file ownership and dependencies. All agents leave Git operations to the learner, while GitHub Copilot CLI in the Codespace coordinates the work.
