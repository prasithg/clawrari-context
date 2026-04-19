# FRD-C2 Blueprint Prep — Memory Architecture

**Date:** 2026-04-09
**Status:** Drafted during Night Work Session 1
**Recommendation:** Blueprint FRD-C2 before FRD-C3

## Why C2 goes first

FRD-C2 should go first. Not because it's more glamorous, but because it's load-bearing.

C3 Self-Improvement depends on C2 in at least four ways:
- failures, regressions, feedback, and learnings need durable storage locations
- startup review loops need deterministic memory loading
- semantic retrieval is required for useful review instead of blind file scans
- session continuity is the substrate for any "assistant gets better over time" claim

If we blueprint C3 first, we risk designing loops that have nowhere clean to live.

## Proposed Blueprint Scope

Blueprint FRD-C2 as four implementation work orders, sequenced from infrastructure to behavior.

### WO-C2-001: Memory Workspace Bootstrap
**Goal:** Create the standard file/folder structure, seed templates, and validation routine.

**Core deliverables:**
- `bootstrap/` or installer step that creates memory workspace
- seed templates for `session-brief.md`, `subagent-ledger.md`, `projects.md`, `people.md`, `preferences.md`, `regressions.md`, `context-holds.md`, `predictions.md`, `reference/conventions.md`
- `MEMORY.md` memory index template
- validation command for missing core files

**Primary requirements covered:**
- REQ-MEM-001
- REQ-MEM-005
- REQ-MEM-006

### WO-C2-002: Deterministic Startup + Session Brief System
**Goal:** Enforce startup read order and bounded session-brief maintenance.

**Core deliverables:**
- startup loader spec and implementation hooks
- session-type branching (main vs non-main)
- brief size-budget validator
- session-end rewrite workflow or helper
- stale subagent scan during startup

**Primary requirements covered:**
- REQ-MEM-002
- REQ-MEM-003
- REQ-MEM-009
- partial REQ-MEM-012

### WO-C2-003: Daily Logs + Durable Memory Maintenance
**Goal:** Make recency capture, promotion, and maintenance explicit and lightweight.

**Core deliverables:**
- daily log creation/append workflow
- promotion rules from daily logs into durable memory
- context-hold expiry check
- maintenance SOP for rewrite/reindex/promote cadence

**Primary requirements covered:**
- REQ-MEM-004
- REQ-MEM-010
- REQ-MEM-011
- REQ-MEM-012

### WO-C2-004: Local Semantic Retrieval Layer
**Goal:** Add search-first, read-second retrieval over human-readable memory files.

**Core deliverables:**
- chunking/indexing pipeline for memory docs
- SQLite metadata store
- local embedding workflow
- retrieval API returning path + line metadata
- reindex/freshness command and stale-index detection

**Primary requirements covered:**
- REQ-MEM-007
- REQ-MEM-008
- partial REQ-MEM-012

## Recommended build order
1. WO-C2-001
2. WO-C2-002
3. WO-C2-003
4. WO-C2-004

This order matches reality. File structure first, startup behavior second, maintenance third, retrieval last.

## Blueprint decision notes
- The semantic layer should augment plain-text files, never replace them.
- The startup sequence must be machine-checkable, not just documented in prose.
- Session brief size limits should be enforced mechanically.
- Subagent ledger continuity belongs in memory, not hidden runtime state.
- Context holds must stay distinct from durable rules/preferences or the system becomes cluttered fast.

## What the full blueprint should contain next
- one ExecPlan for FRD-C2 implementation
- four 8090 work orders mapped to the requirements above
- acceptance criteria traced from REQ-MEM-001 through REQ-MEM-012
- explicit repo paths once the Clawrari implementation repo/module layout is chosen
- a pre-build spec review pass before spawning a coding agent

## Recommendation for Prasith
If you want the shortest path to a credible Clawrari v1, pick **C2 Memory Architecture** as the first blueprint. It's the base layer. C3 gets easier and better once C2 is real.
