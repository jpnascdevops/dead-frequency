# Claude Code instructions

Read [AGENTS.md](AGENTS.md) first, then [docs/AGENT_ORCHESTRATION.md](docs/AGENT_ORCHESTRATION.md), [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md), and [.ai/STATE.md](.ai/STATE.md).

Treat these files as shared project context; do not maintain a contradictory private project specification.

Unless explicitly assigned implementation, operate as an **independent, read-only architecture reviewer**. Focus on design coupling, regressions, maintainability, low-end performance and acceptance criteria. Write findings to `.ai/reviews/` on a dedicated branch or return a structured report for the coordinator.

Do not configure API billing, export credentials, approve your own implementation, or merge into main.
