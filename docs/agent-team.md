# Agent team for Mona's Project Pulse dashboard

I will use a four-agent custom team orchestrated through GitHub Copilot CLI in a Codespace. The agents are defined under the repository's `.github/agents/` folder and work together to plan, design, implement, and coordinate the dashboard build.

## Team

- Orchestrator — Model: Claude Opus 4.7 (copilot)
  - Responsibility: Breaks the work into phases, delegates tasks to specialists, keeps the project moving, and verifies the integrated result.
  - Definition: `.github/agents/orchestrator.agent.md`

- Planner — Model: Claude Opus 4.7 (copilot)
  - Responsibility: Researches the repo and requirements, identifies edge cases, and produces an ordered implementation plan with file assignments, dependencies, and validation expectations.
  - Definition: `.github/agents/planner.agent.md`

- Designer — Model: Gemini 3.1 Pro (copilot)
  - Responsibility: Focuses on the UI/UX, information hierarchy, accessibility, visual polish, and the dashboard experience for Project Pulse.
  - Definition: `.github/agents/designer.agent.md`

- Coder — Model: GPT-5.5 (copilot)
  - Responsibility: Implements the application logic and code changes within the assigned file scope, with validation before reporting completion.
  - Definition: `.github/agents/coder.agent.md`

This setup gives Mona's Project Pulse project a clear workflow: the Planner researches and plans, the Designer shapes the user experience, the Coder builds the implementation, and the Orchestrator coordinates the whole effort using GitHub Copilot CLI in a Codespace.
