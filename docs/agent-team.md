# Agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate the custom agent team building Mona's Project Pulse dashboard. Each agent has a focused responsibility and an explicit target model:

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 (copilot) | Coordinates the team, turns the request into phases, delegates work with explicit file scopes, manages dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository and relevant documentation, identifies dependencies, edge cases, risks, and validation needs, then produces the implementation plan. | `.github/agents/planner.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements dashboard logic and assigned application changes with clear, testable code, explicit errors, and the required runnable-app support configuration. | `.github/agents/coder.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Defines and implements the Project Pulse user experience, including information hierarchy, accessibility, responsive behavior, visual clarity, project cards, status badges, priority treatment, and dashboard styling. | `.github/agents/designer.agent.md` |

The Orchestrator will typically request a plan first, delegate design and implementation work according to non-overlapping file scopes, and then verify that the final dashboard works as a cohesive experience.
