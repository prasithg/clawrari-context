# Blueprint Session Notes — FRD-C2 Memory Architecture

**Date:** 2026-04-10
**Session type:** Blueprint drafting (Claude Code, Opus 4.6)
**Output:** `features/blueprint-c2-memory-architecture.md`

---

## What Was Done

Drafted the complete FRD-C2 implementation blueprint containing:
- 1 ExecPlan covering the full FRD-C2 scope
- 4 work orders (WO-C2-001 through WO-C2-004) in 8090 format
- Requirements traceability matrix mapping all 12 REQ-MEM requirements
- "Exists vs Needs Building" analysis comparing live workspace to gaps
- Build order with dependency diagram

## Key Decisions Made

1. **influences.md excluded from v1** — audit marked it "too personal." Optional extension later.

2. **30-day rolling window for daily log indexing** — keeps index manageable. Older logs accessible via direct file read but not semantically indexed by default.

3. **Manual promotion in v1** — daily log content doesn't auto-promote to durable memory. Assistant suggests, operator decides. Prevents noise pollution in long-term files.

4. **"Main session" as default session type** — when detection is ambiguous, load more context (safe) rather than less (risky).

5. **Expiry detection surfaces but doesn't auto-delete holds** — operator review before removal. Avoids losing context prematurely.

6. **Graceful degradation for missing files** — startup warns and continues rather than crashing. Partially loaded > not loaded.

## Assumptions

- The live workspace (Prasith's current setup) is the authoritative reference implementation. The blueprint packages it, doesn't redesign it.
- QMD is the semantic retrieval tool and will remain the default for v1. The spec documents its integration, not a replacement.
- Clawrari's bootstrap command will exist as part of FRD-C1 (Core Architecture). WO-C2-001 specifies the memory-specific bootstrap steps that integrate with it.
- File paths match the current workspace layout exactly. No directory restructuring needed.

## Discoveries

- **Session brief is over budget:** The current `memory/session-brief.md` is 66 lines — 16 lines over the stated 50-line budget. This validates the need for a mechanical size-budget validator (WO-C2-002).
- **The gap is formalization, not invention:** Every component in the FRD already exists in some form. The work is packaging, not building from scratch.
- **Context-hold expiry is pure convention today:** No tooling checks whether holds have expired. This is the most net-new functionality in the blueprint.
- **QMD freshness check is the only formalized part of the retrieval layer:** Everything else (chunking, schema, API contract) is implicit.

## Open Items for Review

- [ ] Should the bootstrap create a first daily log file for the install date, or wait for the first notable event?
- [ ] What's the right default embedding model for local QMD indexing? (Needs to be specified in WO-C2-004 before implementation.)
- [ ] Should the validation command also check file format/structure, or just existence?
- [ ] Is 30 days the right default for daily log indexing window, or should it be configurable from the start?
- [ ] HEARTBEAT.md integration: should the maintenance SOP (WO-C2-003) be a separate file referenced from HEARTBEAT.md, or merged into it?

## Files Read

- `features/frd-c2-memory-architecture.md` — full FRD (12 requirements)
- `features/frd-c2-blueprint-prep-2026-04-09.md` — 4 work orders decomposition
- `features/setup-audit-2026-04-08.md` — current setup readiness catalog
- `reference/execplan-template.md` — ExecPlan format template
- `AGENTS.md` — current startup instructions
- `MEMORY.md` — current memory index
- `memory/session-brief.md` — current session brief (66 lines, over budget)
- `HEARTBEAT.md` — current maintenance routines

## Files Written

- `features/blueprint-c2-memory-architecture.md` — the blueprint (this session's primary deliverable)
- `notes/blueprint-c2-session-notes.md` — this file
