# Dead Frequency

**Dead Frequency** is an experimental single-player zombie survival FPS, currently prototyped with Three.js, Vite and WebGL. The long-term direction is a semi-open-world survival experience with vehicles, fuel, exploration, radio transmissions and responsive combat.

## Status

- A playable Blackridge indoor prototype has been reported by Kimi (latest reported build ID: `c9b460b`).
- **The game source has not been imported into this repository yet.** Reported implementation details must be verified against the actual exported files.
- Kimi is working on a subsequent game iteration. Do not overwrite or publish incomplete in-progress work.

## AI development workflow

The main setup and implementation handoff is in [docs/AGENT_ORCHESTRATION.md](docs/AGENT_ORCHESTRATION.md). All coding agents must read [AGENTS.md](AGENTS.md) first.

**Goal:** coordinate Hermes Agent, Kimi Code, Codex CLI and Claude Code using the owner's existing subscriptions, without adding separately billed inference APIs. Validate each official client's authentication, headless operation and terms; never bypass usage limits. Git is the source of truth, and human approval is required before merging gameplay changes.

## Getting started

1. Export the **complete, latest** Kimi project after its ongoing task is finished.
2. Add source files to a new branch; avoid force-pushes, secrets and generated build outputs.
3. Have an agent inspect the real project, update `docs/ARCHITECTURE.md` and `.ai/STATE.md`, and execute its build/tests.
4. Implement the multi-agent workflow according to the orchestration spec, beginning with a read-only dry run.
