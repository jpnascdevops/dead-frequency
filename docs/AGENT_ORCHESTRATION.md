# Dead Frequency — Multi-Agent Development Orchestration

**Version:** 0.1 — implementation handoff, 2026-11-09  
**Repository:** https://github.com/jpnascdevops/dead-frequency  
**Owner's non-negotiable requirement:** leverage **existing subscriptions only** (ChatGPT/Codex, Claude/Claude Code, Kimi/Kimi Code). **No additional inference API billing.**

> IMPORTANT: This document is a specification, **not evidence that the automation is installed or working**. The repository was initialized with documentation only. The game's current source must be exported from Kimi after the in-progress iteration finishes.

## 1. Mission and success criteria

Create a reusable **local-first, Git-backed multi-agent development workflow** starting with Dead Frequency and later usable for personal and, only when authorized, professional projects.

An agent assigned a ticket can safely implement on its own branch; a different agent reviews an immutable diff; design/architecture are checked; tests and handoffs are archived; all agents see accurate task state without sharing private chat transcripts. Human controls final acceptance/merge.

**Must prove with one complete dry-run and one approved small change:**
1. Independent CLI availability/authentication is checked, without exposing tokens.
2. Each adapter uses allowed access under its own existing subscription, or explicitly reports unavailable/manual. No hidden paid fallback.
3. Fresh-session agents recover project context from docs and task metadata.
4. Single-writer worktree isolation, exact-SHA reviews and state transitions work.
5. Agent outputs and failed commands are captured, timeouts/budgets enforced, and the coordinator stops on errors.
6. Build/test evidence is distinguishable from unverified agent assertions.
7. No unattended merge or destructive shell actions occur.
8. Docs and a repeatable local setup path exist.

## 2. Architecture

```text
Owner / local command
        |
        v
Hermes Agent (optional coordinator, memory, task routing)
        |
        +-- task ledger (.ai/tasks/*.json; GitHub issues optional)
        +-- reusable project memory (AGENTS.md, docs/, .ai/STATE.md)
        +-- adapters (separate CLI processes, capability-gated)
        |    +-- Kimi Code CLI     -> implementer (when supported)
        |    +-- Codex CLI         -> reviewer / implementer
        |    +-- Claude Code CLI   -> architecture reviewer
        |
        +-- isolated Git worktrees + branches
        +-- tests, artifacts and structured reviews
        |
        v
Human approval -> PR -> merge
```

Hermes is **not** the authority on code truth; Git commit SHAs and test artifacts are. Hermes native task boards/memory may be used **only after verifying their currently installed commands and behavior**. Do not depend on undocumented internal interfaces. Begin with a small local coordinator if Hermes compatibility blocks subscription-only execution.

**Crucial distinction:** Do not confuse using Kimi *membership credentials through an officially supported integration* with purchasing Moonshot API credits. Verify both the allowed membership endpoint and actual quota accounting before enabling. Never silently switch to billed endpoints.

## 3. Source of truth and shared memory

- `AGENTS.md` — shared behavior and safety contract; `CLAUDE.md` — Claude-specific entry point.
- `docs/GAME_DESIGN.md` — confirmed creative direction and scope.
- `docs/ARCHITECTURE.md` — architecture, updated **after reading actual source**.
- `docs/DECISIONS.md` — dated ADRs with context/trade-offs; append rather than rewriting history.
- `.ai/STATE.md` — concise current truth, ongoing work, blockers, next actions.
- `.ai/tasks/DF-AI-001.json` — sample machine-readable ticket.
- `.ai/reviews/` — review reports bound to a commit SHA.
- `.ai/handoffs/` — implementation handoffs and verification.
- GitHub issues/PRs — collaboration and approval, not a replacement for source-state checks.

**Read ordering:** AGENTS.md → state → task → relevant design/architecture → source/diff. Supply only relevant snippets to reduce context consumption. Never dump all prior sessions.

**Concurrency:** task status is mutable in a local coordinator's SQLite database or single-writer journal; committed task files represent durable checkpoints. Avoid multiple agents editing a shared JSON simultaneously. Atomic updates + file locks or a database transaction are required. On crash, reconcile against Git.

**Context invalidation:** store the branch, base SHA, head SHA, timestamp and reviewed file list. Any new commit invalidates prior approvals until reviewed again. Memory should be concise and verifiable.

## 4. Agents and adapters

| Adapter | Initial role | Mode | Boundaries |
|---|---|---|---|
| Kimi Code CLI | Implementer | Official, verified non-interactive CLI | Own worktree, bounded writes |
| Codex CLI | Code/performance reviewer | Official CLI, read-only sandbox where supported | No modifying implementation branch |
| Claude Code CLI | Architecture/UX reviewer | Official CLI non-interactive, no writes allowed | No access to Kimi/Codex credentials |
| Hermes Agent | Coordinator + optional model-driven planning | Only officially supported subscription-backed configuration | May be disabled/manual if integration does not satisfy cost constraint |

**Never assume all three can be called in exactly the same way on every platform or version.** For each adapter implement:
- `probe`: verify executable, version, authentication availability/capability *without printing secrets*.
- `run`: invoke only documented flags; use process argument arrays rather than shell string interpolation; provide explicit cwd/worktree.
- `limits`: max runtime, per-task invocation count, concurrency = 1 initially, bounded retry count = 1, no auto-top-up.
- `capture`: stdout/stderr (scrub credentials), exit code, start/end time, tool version, commit SHA; preserve evidence.
- `abort`: terminate descendants and release lock/worktree on failure.
- `manual_fallback`: generate a prompt/handoff file to be executed in the official app if automation is unsupported.

Do not store passwords, session cookies, OAuth tokens, API keys or model transcripts in Git. In CI, do not attempt to impersonate a personal subscription with copied credentials.

**Possible subscription constraints:** ChatGPT Plus and Claude Pro access to their coding clients does not automatically grant API inference credits; Hermes may have native adapters with different authentication terms. Verify the actual installed integration and account terms. No claim of complete automated access until tested.

## 5. Execution pipeline

1. **Discover:** on first run, read repository, locate actual game source and tests, probe CLIs; produce a compatibility matrix. Do not run code-modifying agents.
2. **Plan:** create ticket with scope, files, risks, test commands and acceptance criteria. Owner confirms when change has material scope.
3. **Lock:** coordinator atomically claims task; create feature branch and isolated worktree from latest approved main.
4. **Implement:** Kimi receives task-specific prompt and shared context, works **only** in that worktree; runs targeted tests. Obtain commit SHA.
5. **Review:** Codex reviews immutable `base...head` diff read-only; Claude independently reviews architecture/maintainability. Parallel review allowed only after implementation commit is frozen.
6. **Judge:** parse structured results (finding severity, file/line, reproduction, risk); fail closed on missing/error review. Do not count unverified text as test success.
7. **Fix:** send consolidated actionable findings to original implementer; **maximum 1 automated fix cycle in v0.1**. Re-run tests and both reviews on changed SHA.
8. **Human gate:** present PR with summary, tests, screenshots/FPS measurements if applicable; request explicit acceptance. No automatic main merge.
9. **Handoff:** update task record, STATE, decisions and audit log with final commit and outstanding follow-ups.

Suggested state machine:
```
backlog -> ready -> in_progress -> review
                                     |-> changes_requested -> in_progress
                                     |-> approved -> human_accepted -> merged
Any state -> blocked | cancelled
```

Avoid deadlocks: assign an owner; disallow simultaneous edits to the same task; add lease expiry and recovery. Avoid endless agent debates: two independent reviews maximum in v0.1, one rework pass, then escalate.

## 6. Suggested portable implementation

Create an optional `tools/agent_orchestrator/` Python package with:
- `config.example.toml` (repo root, modes, allowed executables, maximum runs, timeouts; **no secrets**).
- `adapters/base.py`, `kimi.py`, `codex.py`, `claude.py`; capability-based provider discovery.
- `git_worktrees.py` with safe branch/worktree creation and cleanup.
- `ledger.py` using SQLite transactions or atomic JSON writes and task locks.
- `pipeline.py` with explicit lifecycle, prompt construction and review validation.
- `cli.py`: `doctor`, `plan`, `dry-run`, `run-task`, `status`, `reconcile`.
- `tests/`: test adapters with fake CLI scripts; test retries, shutdown, malicious output, concurrency, SHA invalidation.

**Do not install Hermes or run remote install scripts automatically.** Document official installation steps for Windows/WSL2; ask owner to execute and authorize required changes. If Hermes itself needs unsupported billing, preserve the integration behind a disabled feature flag and use an ordinary deterministic Python coordinator: the workflow can still run on the authenticated coding CLIs. An LLM coordinator is a convenience, not a dependency.

**Security defaults:**
- Read-only discovery until owner explicitly enables edits.
- No generic unrestricted shell or automatic deletion.
- Environment variable allowlist; redact credentials from logs.
- Disable Git pushes by default until the user approves a repo/branch target.
- Never treat LLM-written task output as executable commands.
- Worktree permissions limited to repository and build temp directories.
- No external distribution or telemetry by default.
- Default local-only execution. No remote actions without consent.

## 7. Task contract

Task JSON schema (v0.1 example in `.ai/tasks/DF-AI-001.json`) must include: `id`, `title`, `state`, `owner_agent`, `review_agents`, `base_sha`, `head_sha`, `branch`, `acceptance_criteria`, `test_commands`, `attempts`, `last_updated`, `blockers`. Null values are valid before execution. Keep outputs machine-readable, e.g.:

```json
{
  "task_id": "DF-AI-001",
  "agent": "codex",
  "reviewed_sha": "<commit SHA>",
  "verdict": "changes_requested",
  "findings": [
    {"severity": "high", "path": "src/game/weapon.js", "line": 42, "issue": "Example only", "evidence": "Reproduction steps"}
  ]
}
```

Review reports should distinguish `pass`, `fail`, `not_run` and `unknown`. A successful CLI exit does not prove the application passes tests.

## 8. First test — DF-AI-001

**Title:** Validate cross-agent shared context and read-only review, with NO game modifications.

- Export and commit actual source to a dedicated import branch after Kimi finishes.
- On a local clone, run `doctor`; create an inventory of binary versions, authenticated CLI capabilities, and quota-related caveats.
- Generate a current architecture summary from the imported code; user checks it.
- Execute Kimi, Codex and Claude each on a **read-only explanation/review** task from fresh sessions. Verify all three can identify the same project version and task.
- Record outputs in separate local reports, summarize without leaking chats.
- Emulate a code change on a test fixture branch; trigger two independent reviewers against an immutable SHA, then simulate a changed commit and show prior approval invalidation.
- Verify no API purchases, external billing, main merges or game file modifications occurred.

After successful dry-run, ask owner approval for a small real task (e.g., weapon recoil telemetry or audio toggle regression test), with Kimi implementing and Codex/Claude reviewing.

## 9. Acceptance checklist for implementation agent

- [ ] Repo source state inventoried; no game source invented.
- [ ] Official client compatibility table with observed evidence and blockers.
- [ ] No unapproved billable API use; no credential handling shortcuts.
- [ ] Working task ledger, resume/recovery and worktree isolation.
- [ ] Fresh-session context handoffs for all three agents.
- [ ] Read-only reviewers bound to SHA; approvals expire on changes.
- [ ] One bounded correction cycle; failures stop safely.
- [ ] Human approval before PR merge and other destructive acts.
- [ ] Tests for mocked execution, failures, concurrency and security boundaries.
- [ ] README, setup, operations and rollback documentation.
- [ ] Reproducible dry-run transcript (redacted) and honest list of unfinished capabilities.

## 10. Future reuse in professional projects

Keep project context, memory stores, auth scopes, audit logs and Git remotes isolated. Do **not** mix company data with personal subscription prompts unless permitted by employer policies. Build per-project profiles and secret-free reusable skills. Never auto-copy project memory between work and hobby contexts.

## 11. Useful reference points (verify current versions)

- Hermes Agent source/docs: https://github.com/NousResearch/hermes-agent
- Kimi Code documentation: https://www.kimi.com/code/docs/
- Claude Code documentation: https://code.claude.com/docs/
- Codex documentation: https://developers.openai.com/codex/
- Git worktrees: https://git-scm.com/docs/git-worktree

**Agent instruction:** Verify each integration against these official sources and the installed binaries, report discrepancies, and implement only what is documented and permitted under the user's subscriptions.
