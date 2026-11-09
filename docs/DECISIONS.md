# Architecture decisions

Append decisions as dated entries. Do not silently rewrite history.

## ADR-001 (2026-11-09): Source of truth
**Decision:** Git commits, repository docs, tickets and verifiable test evidence are authoritative; no automatic syncing of private chat memory.  
**Reason:** Reproducible, auditable, portable across model vendors.

## ADR-002 (2026-11-09): Subscription-only agent operation
**Decision:** Use existing officially authenticated Kimi Code, Codex CLI and Claude Code wherever permitted; no separately billed inference API. If headless or Hermes integration is unsupported, fall back to manual handoff.  
**Reason:** Keep incremental cost at zero and respect provider boundaries.

## ADR-003 (2026-11-09): Human gate
**Decision:** Require human approval to merge, deploy or execute destructive operations. Agents may review independent immutable SHAs.  
**Reason:** Protect code ownership and quality.

## ADR-004 (2026-11-09): Keep prototype stack until evaluated
**Decision:** Do not migrate Three.js to Godot/Unity automatically. Finish source export, gameplay validation and performance measurement first.  
**Reason:** Avoid unnecessary rewrite while Kimi is developing the current iteration.
