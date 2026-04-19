# ExecPlan: FRD-C2 Memory Architecture — Implementation Blueprint

_This ExecPlan is a living document. Progress, Surprises, Decision Log, and Outcomes must be kept current._

Maintained per: `reference/execplan-template.md`

**FRD:** `features/frd-c2-memory-architecture.md`
**Prep:** `features/frd-c2-blueprint-prep-2026-04-09.md`
**Audit:** `features/setup-audit-2026-04-08.md`
**Date drafted:** 2026-04-10
**Status:** Draft — awaiting review

---

## Purpose / Big Picture

After this work ships, a fresh Clawrari install bootstraps a complete memory workspace, loads context in a deterministic startup sequence, captures daily activity, maintains durable thematic knowledge, and retrieves prior decisions via local semantic search — all backed by human-readable files the operator can inspect and edit directly. The assistant feels continuous across sessions without requiring the user to re-explain anything.

---

<!-- ═══════════════════════════════════════════════════
     MACHINE-PARSED SECTIONS
     ═══════════════════════════════════════════════════ -->

<context>
Project: Clawrari — OpenClaw-based assistant operating system
Workspace: /Users/prasithgovin/.openclaw/workspace/
FRD: features/frd-c2-memory-architecture.md (REQ-MEM-001 through REQ-MEM-012)

Key files (current live system — the reference implementation):
- AGENTS.md — startup instructions (read SOUL → USER → session-brief → ledger → daily logs → MEMORY.md)
- MEMORY.md — memory index mapping all memory files and their purposes
- HEARTBEAT.md — maintenance routines including QMD freshness, RLHF checks, config health
- memory/session-brief.md — 50-line preconscious buffer, updated at session end
- memory/subagent-ledger.md — append-only spawned-task tracker
- memory/YYYY-MM-DD.md — daily raw activity logs
- memory/projects.md, people.md, preferences.md — durable thematic files
- memory/regressions.md — failure-to-guardrail pipeline
- memory/rules-constitutional.md — 10 hard rules (high stability)
- memory/rules-tactical.md — evolving conventions (lower stability)
- memory/context-holds.md — temporary instructions with expiry
- memory/predictions.md — prediction-outcome calibration
- memory/influences.md — articles/people that shaped the system
- memory/reference/conventions.md — type-tag + trust score conventions
- memory/heartbeat-state.json — state tracking for periodic checks
- scripts/qmd-reindex.sh — QMD semantic memory reindex script

Current state: The live workspace IS the reference implementation. Every memory file, startup sequence, and maintenance workflow described in the FRD already exists in Prasith's workspace. What does NOT exist is the packaging: bootstrap scaffolding, seed templates, validation tooling, formal startup spec, documented maintenance SOPs, and a retrieval layer spec that can be reproduced on a fresh install.

Recent decisions:
- (2026-04-09) C2 blueprinted before C3 because memory is the substrate for self-improvement
- (2026-04-08) Setup audit confirmed memory architecture is Clawrari's strongest differentiator
- (2026-04-01) Rules split into constitutional (hard) and tactical (evolving); stale content archived
</context>

<constraints>
- This is a DESIGN DOCUMENT. No code implementation in this plan.
- Do NOT redesign what already works — formalize and package the existing patterns.
- File-first, local-first. Semantic layer augments plain-text files, never replaces them.
- No cloud-only dependencies. Everything must work offline.
- Memory files must remain human-readable and directly editable.
- Do NOT delete existing content or files unless explicitly listed as a task step.
- Do NOT add new dependencies without documenting the rationale.
- XML sections in work orders are for future coding agent consumption.
- influences.md is skipped for Clawrari v1 (too personal per audit).
- Seed templates must be content-neutral (no Prasith-specific data).
</constraints>

<acceptance_criteria>
This work is complete when:
- [ ] `clawrari bootstrap` (or equivalent) creates a complete memory workspace from seed templates
- [ ] A fresh session loads context in documented, deterministic order
- [ ] Session brief stays within enforced line budget
- [ ] Daily logs are created, appended, and read during startup
- [ ] All durable memory categories have seed templates with documented purpose
- [ ] MEMORY.md index is generated and stays in sync with actual files
- [ ] Subagent ledger tracks spawned tasks and surfaces stale entries at startup
- [ ] Regressions, context holds, and predictions have structured capture workflows
- [ ] Semantic search returns ranked snippets with source path + line metadata
- [ ] Reindex workflow detects and corrects stale semantic indexes
- [ ] Maintenance SOP documents when to rewrite brief, append logs, promote to durable, and reindex
- [ ] All 12 REQ-MEM requirements are satisfied and traced to work orders
- [ ] No regressions in existing workspace functionality
</acceptance_criteria>

---

## What Already Exists vs What Needs Building

Understanding what's live is critical. The current workspace is the reference implementation — this blueprint packages it for reproducibility.

### Already exists and working (formalize, don't rebuild)

| Component | Location | Status |
|-----------|----------|--------|
| Session brief | `memory/session-brief.md` | ✅ Live, 50-line budget, updated at session end |
| Subagent ledger | `memory/subagent-ledger.md` | ✅ Append-only, checked at startup |
| Daily logs | `memory/YYYY-MM-DD.md` | ✅ Date-named, read today+yesterday |
| Startup sequence | `AGENTS.md` lines 10-19 | ✅ Ordered: SOUL → USER → brief → ledger → daily → MEMORY.md |
| Memory index | `MEMORY.md` | ✅ Maps all memory files with descriptions |
| Durable files | `memory/{projects,people,preferences,regressions,rules-*,context-holds,predictions}.md` | ✅ All exist with content |
| Reference conventions | `memory/reference/conventions.md` | ✅ Type-tag + trust score system |
| QMD reindex | `scripts/qmd-reindex.sh` | ✅ Triggered when stale >24h |
| QMD freshness check | `HEARTBEAT.md` line 7 | ✅ Runs every heartbeat |
| Heartbeat state | `memory/heartbeat-state.json` | ✅ Tracks periodic check timestamps |

### Needs building for Clawrari

| Component | What's Missing |
|-----------|---------------|
| Bootstrap scaffolding | No installer/generator creates the workspace from scratch |
| Seed templates | Existing files have Prasith-specific content; need content-neutral templates |
| Workspace validation | No command to check for missing/malformed core files |
| Formal startup spec | AGENTS.md is prose; need machine-checkable startup contract |
| Session-type detection | Main vs subagent session branching is implicit, not formalized |
| Brief size-budget validator | 50-line budget is a convention, not enforced mechanically |
| Brief rewrite rubric | No documented criteria for what stays/goes during rewrite |
| Daily log generator | Date-naming is manual; need a creation/append helper |
| Promotion workflow | No documented rules for when daily log content becomes durable |
| Regression template | Regressions exist but capture format isn't standardized for new installs |
| Context-hold expiry parser | Expiry dates exist but no automated detection of expired holds |
| Maintenance SOP | HEARTBEAT.md covers it partially; need explicit cadence documentation |
| Retrieval API contract | QMD works but the search→read contract isn't formalized |
| Reindex freshness spec | Script exists but freshness detection criteria aren't documented |

---

## Build Order and Dependencies

```
WO-C2-001: Memory Workspace Bootstrap
    ↓ (provides file structure that all others depend on)
WO-C2-002: Deterministic Startup + Session Brief System
    ↓ (startup reads the files created by 001; brief maintenance feeds into 003)
WO-C2-003: Daily Logs + Durable Memory Maintenance
    ↓ (maintenance workflows produce content that 004 indexes)
WO-C2-004: Local Semantic Retrieval Layer
```

**WO-C2-001** is fully independent.
**WO-C2-002** depends on WO-C2-001 (needs files to exist before defining load order).
**WO-C2-003** depends on WO-C2-001 (needs file taxonomy) and WO-C2-002 (brief rewrite is part of maintenance).
**WO-C2-004** depends on WO-C2-001 (needs files to index) and WO-C2-003 (maintenance triggers reindex).

---

## Requirements Traceability Matrix

Every FRD requirement mapped to the work order(s) that satisfy it.

| Requirement | Description | Primary WO | Secondary WO |
|-------------|-------------|------------|--------------|
| REQ-MEM-001 | Standard Memory Workspace Structure | **WO-C2-001** | — |
| REQ-MEM-002 | Deterministic Session Startup Sequence | **WO-C2-002** | — |
| REQ-MEM-003 | Bounded Session Brief | **WO-C2-002** | WO-C2-003 (rewrite in maintenance) |
| REQ-MEM-004 | Daily Log Capture | **WO-C2-003** | WO-C2-001 (seed template) |
| REQ-MEM-005 | Durable Thematic Memory Files | **WO-C2-001** | WO-C2-003 (promotion workflow) |
| REQ-MEM-006 | Memory Index and Discoverability | **WO-C2-001** | — |
| REQ-MEM-007 | Semantic Retrieval Layer | **WO-C2-004** | — |
| REQ-MEM-008 | Search-Then-Read Retrieval Workflow | **WO-C2-004** | — |
| REQ-MEM-009 | Subagent Continuity Ledger | **WO-C2-002** | WO-C2-001 (seed template) |
| REQ-MEM-010 | Regression Capture and Guardrail Learning | **WO-C2-003** | WO-C2-001 (seed template) |
| REQ-MEM-011 | Context Holds with Expiry | **WO-C2-003** | WO-C2-001 (seed template) |
| REQ-MEM-012 | Memory Maintenance and Freshness Workflow | **WO-C2-003** | WO-C2-002 (brief rewrite), WO-C2-004 (reindex) |

**Coverage:** 12/12 requirements covered. No orphans.

---

## Work Orders

---

### WO-C2-001: Memory Workspace Bootstrap

**Objective:** Create the standard file/folder structure, seed templates, memory index, and workspace validation routine so a fresh Clawrari install has a complete, working memory system from first boot.

**Scope:** File system scaffolding, seed template authoring, index generation, validation command. No runtime behavior — this is pure structure.

**Estimated complexity:** Medium. Mostly file authoring and a validation script. The design decisions are already made by the live workspace; this packages them.

<context>
Work order: WO-C2-001 — Memory Workspace Bootstrap
Project: Clawrari Memory Architecture (FRD-C2)

Reference implementation (current live workspace):
- memory/session-brief.md — 50-line preconscious buffer
- memory/subagent-ledger.md — append-only agent tracker
- memory/projects.md — active project context
- memory/people.md — people and relationships
- memory/preferences.md — operator preferences
- memory/regressions.md — failure-to-guardrail records
- memory/rules-constitutional.md — hard rules (high stability)
- memory/rules-tactical.md — evolving conventions (lower stability)
- memory/context-holds.md — temporary instructions with expiry
- memory/predictions.md — prediction-outcome calibration
- memory/reference/conventions.md — type-tag + trust score conventions
- memory/heartbeat-state.json — periodic check state
- MEMORY.md — top-level memory index

What exists: All files above exist with Prasith-specific content.
What's missing: Content-neutral seed templates, bootstrap script, validation command.
</context>

<constraints>
- Seed templates must be content-neutral — no personal data, names, or project-specific references.
- Each seed template must include a header comment explaining what belongs in the file and what doesn't.
- File paths and naming must match the live workspace exactly (proven patterns).
- influences.md is excluded from v1 seed templates (per audit: too personal).
- Bootstrap must be idempotent — running it again on an existing workspace must not overwrite user content.
- Do NOT create empty files — every seed template must have structural scaffolding.
</constraints>

<acceptance_criteria>
This work order is complete when:
- [ ] Bootstrap command creates the full directory tree: memory/, memory/reference/
- [ ] Seed templates exist for all core files: session-brief.md, subagent-ledger.md, projects.md, people.md, preferences.md, regressions.md, rules-constitutional.md, rules-tactical.md, context-holds.md, predictions.md, reference/conventions.md
- [ ] Each seed template has a purpose header, structural scaffolding, and example entries
- [ ] MEMORY.md template is generated with correct file paths and descriptions
- [ ] heartbeat-state.json seed is created with sensible defaults
- [ ] Validation command checks for missing core files and reports which are missing
- [ ] Bootstrap is idempotent — existing files are not overwritten
- [ ] Command: `clawrari validate memory` → lists missing files or reports "Memory workspace intact"
</acceptance_criteria>

#### Implementation Steps

1. **Define the canonical file tree**

   ```
   memory/
   ├── session-brief.md          # Preconscious buffer (50-line max)
   ├── subagent-ledger.md        # Append-only task tracker
   ├── projects.md               # Active project context
   ├── people.md                 # People and relationships
   ├── preferences.md            # Operator preferences
   ├── regressions.md            # Failure-to-guardrail pipeline
   ├── rules-constitutional.md   # Hard rules (rarely change)
   ├── rules-tactical.md         # Evolving conventions
   ├── context-holds.md          # Temporary instructions with expiry
   ├── predictions.md            # Prediction-outcome calibration
   ├── heartbeat-state.json      # Periodic check state
   ├── reference/
   │   └── conventions.md        # Memory file conventions, type-tags, trust scores
   └── YYYY-MM-DD.md             # Daily logs (created as needed, not seeded)
   MEMORY.md                     # Top-level memory index (workspace root)
   ```

2. **Author seed templates** — For each file, create a template with:
   - A `## Purpose` header explaining what belongs here
   - A `## Format` section showing the expected entry structure
   - 1-2 example entries (clearly marked as examples)
   - An empty section ready for user content

   Key template design decisions:
   - `session-brief.md`: Include section headers (Active Context, Hot Tasks, Recent Decisions, Expiring Holds, Known Issues, Carry Forward). Mark the 50-line budget in the file header.
   - `subagent-ledger.md`: Table format with columns: Date, Label, Task, Expected Output, Status (🔄/✅/❌/⏸️), Result Notes.
   - `regressions.md`: Each regression entry has: Name, Date Captured, Context, Failure Mode, Guardrail/Lesson, Status (active/resolved/promoted-to-rule).
   - `context-holds.md`: Each hold has: Hold Name, Created, Expires, Condition, Instruction. Expired holds are moved to an `## Expired` section during maintenance.
   - `predictions.md`: Each prediction has: Prediction, Date, Confidence, Outcome (pending/confirmed/wrong), Resolution Date, Lesson.
   - `rules-constitutional.md`: Numbered list format. Include 2-3 example rules that are universally applicable (e.g., "Never exfiltrate private data", "Ask before destructive actions").
   - `rules-tactical.md`: Grouped by category. Include structural scaffolding with empty categories.
   - `heartbeat-state.json`: JSON with keys for `last_heartbeat`, `last_qmd_reindex`, `last_community_scan`, `last_rlhf_check`, all initialized to `null`.

3. **Author MEMORY.md template** — Table format matching the live version. Each row: file path, description. Include the "How to Use This" section explaining search → get → daily → index workflow.

4. **Build bootstrap logic** — Script or command that:
   - Creates `memory/` and `memory/reference/` directories
   - Copies seed templates into place, skipping any file that already exists
   - Generates MEMORY.md from template if it doesn't exist
   - Writes a bootstrap completion marker (e.g., `.memory-bootstrapped` timestamp file)
   - Outputs a summary of what was created vs skipped

5. **Build validation command** — `clawrari validate memory` that:
   - Checks the canonical file list against what exists on disk
   - Reports missing files with severity (critical vs optional)
   - Checks MEMORY.md index entries against actual files (detects drift)
   - Returns exit code 0 if intact, 1 if issues found

#### Test Strategy

- Run bootstrap on an empty directory → all seed files created, MEMORY.md generated
- Run bootstrap again → no files overwritten, output confirms "already exists"
- Delete 2-3 files → validation reports them as missing
- Add a file not in MEMORY.md → validation warns about undocumented file (stretch)
- Inspect each seed template manually → purpose header present, format section present, example entries present, no personal data

---

### WO-C2-002: Deterministic Startup + Session Brief System

**Objective:** Formalize the session startup read order into a machine-checkable spec, implement session-type branching, enforce the session brief's size budget, define the brief rewrite workflow, and integrate subagent staleness detection into startup.

**Scope:** Startup loader spec, session-type detection, brief validator, brief rewrite rubric, subagent stale-check. This formalizes what AGENTS.md currently describes in prose.

**Estimated complexity:** Medium-High. The behavior is proven but needs formalization into a spec that a coding agent or validation tool can enforce.

<context>
Work order: WO-C2-002 — Deterministic Startup + Session Brief System
Project: Clawrari Memory Architecture (FRD-C2)

Current startup sequence (from AGENTS.md lines 10-19):
1. Read SOUL.md
2. Read USER.md
3. Read memory/session-brief.md (FIRST memory read)
4. Scan memory/subagent-ledger.md for 🔄 running rows; flag stale (>2 hrs) entries
5. Read memory/YYYY-MM-DD.md (today + yesterday)
6. If MAIN SESSION: also read MEMORY.md

Session brief (memory/session-brief.md):
- 50-line max preconscious buffer
- Updated at session end
- Contains: Active Context, Hot Tasks, FRD status, Key Strategy Docs, Current Branches, Carry Forward
- Human-readable, directly editable

Subagent ledger (memory/subagent-ledger.md):
- Append-only table
- Columns: Date, Label, Task, Expected Output, Status, Result Notes
- Status values: 🔄 running, ✅ done, ❌ failed, ⏸️ paused
- Stale threshold: 2 hours with no output file

What exists: The prose startup sequence in AGENTS.md works. The brief and ledger exist.
What's missing: Machine-checkable startup spec, session-type formal definition, brief size enforcement, brief rewrite rubric, startup validation tests.
</context>

<constraints>
- The startup sequence must remain backward-compatible with the current AGENTS.md behavior.
- Session-type detection must not require external service calls.
- Brief size budget is 50 lines (configurable, but 50 is the default).
- The brief rewrite must preserve the section structure (not freeform).
- Subagent staleness threshold is 2 hours (configurable).
- Startup must complete without blocking on failed reads (graceful degradation if a file is missing).
</constraints>

<acceptance_criteria>
This work order is complete when:
- [ ] Startup spec document defines the exact read order with file paths and conditions
- [ ] Session types are formally defined: main session vs subagent session, with branching rules
- [ ] Brief size-budget validator exists: counts lines, warns or truncates when over budget
- [ ] Brief rewrite rubric documents what stays, what gets cut, and priority ordering
- [ ] Subagent staleness detection runs during startup: identifies 🔄 rows older than threshold
- [ ] Startup validation test can verify that a session followed the correct load order
- [ ] Graceful degradation: missing files produce warnings, not crashes
- [ ] Startup spec is machine-parseable (structured format, not just prose)
</acceptance_criteria>

#### Implementation Steps

1. **Write the Startup Spec** — A structured document (e.g., `specs/startup-sequence.md` or YAML) defining:

   ```
   Phase 1 — Identity (always)
     1. Read SOUL.md → required, fail-open with warning
     2. Read USER.md → required, fail-open with warning

   Phase 2 — Hot Context (always)
     3. Read memory/session-brief.md → required, fail-open with warning
     4. Scan memory/subagent-ledger.md → required
        - Filter rows where Status = 🔄
        - For each: check if expectedOutput file exists
        - If row age > staleness_threshold (default: 2h): flag as stale
        - Surface stale entries in session startup notes

   Phase 3 — Recency (always)
     5. Read memory/{today's date}.md → optional (may not exist yet today)
     6. Read memory/{yesterday's date}.md → optional

   Phase 4 — Orientation (main session only)
     7. Read MEMORY.md → required for main sessions
   ```

2. **Define session-type detection** — Rules for distinguishing session types:
   - **Main session:** Direct chat with the human operator. Detected by: presence of interactive user input, absence of a parent task/agent context, or explicit session-type flag.
   - **Subagent session:** Spawned by another agent to complete a delegated task. Detected by: presence of parent task ID, agent-registry entry, or explicit subagent flag.
   - **Cron session:** Triggered by a scheduled job. Detected by: cron execution context flag.
   - Default: If detection is ambiguous, treat as main session (safe default — loads more context, not less).

3. **Design brief size-budget validator** — A utility that:
   - Counts non-empty, non-comment lines in `memory/session-brief.md`
   - Default budget: 50 lines
   - On validation:
     - ≤50 lines: PASS
     - 51-60 lines: WARN ("brief is over budget, consider trimming")
     - >60 lines: FAIL ("brief exceeds budget by N lines, rewrite required")
   - Can be run standalone or as part of startup/maintenance

4. **Document the brief rewrite rubric** — When the brief needs rewriting (session end, major state change), follow this priority order for what stays:

   ```
   Priority 1 (always keep): Active blockers, expiring holds (next 48h), known broken things
   Priority 2 (keep if room): Hot tasks with upcoming deadlines, recent decisions that affect next session
   Priority 3 (keep if room): Active project status summaries, current branch state
   Priority 4 (cut first): Completed items (move to daily log), resolved issues, context older than 7 days
   Structural rule: Maintain section headers even if a section is temporarily empty
   ```

   The rewrite rubric lives alongside the brief template so any agent performing the rewrite can reference it.

5. **Implement subagent staleness detection** — Startup routine that:
   - Parses the ledger table for rows with 🔄 status
   - Computes row age from the Date column
   - Checks if the `Expected Output` path exists on disk
   - Classification:
     - Age > threshold AND no output file → **STALE** (flag prominently)
     - Age > threshold AND output file exists → **LIKELY DONE** (suggest status update to ✅)
     - Age ≤ threshold → **IN PROGRESS** (no action)
   - Stale entries are surfaced at the top of startup notes

6. **Write startup validation test** — A test script or checklist that:
   - Simulates startup by reading files in order
   - Verifies each phase completed (file was read or appropriate warning issued)
   - Checks brief is within budget
   - Checks ledger was scanned
   - Reports pass/fail for each phase

#### Test Strategy

- Full startup on a complete workspace → all phases pass, no warnings
- Startup with missing session-brief.md → Phase 2 warns, continues
- Startup with session-brief.md at 55 lines → WARN issued
- Startup with session-brief.md at 65 lines → FAIL issued
- Startup with stale ledger entry (>2h, no output file) → flagged as stale
- Startup with ledger entry that has output file → suggested as "likely done"
- Subagent session startup → Phase 4 (MEMORY.md) is skipped
- Main session startup → Phase 4 (MEMORY.md) is loaded

---

### WO-C2-003: Daily Logs + Durable Memory Maintenance

**Objective:** Formalize daily log creation and append workflows, define promotion rules from daily logs into durable memory, implement context-hold expiry detection, standardize regression capture, and document the complete maintenance SOP covering brief rewrite cadence, log rotation awareness, promotion triggers, and maintenance scheduling.

**Scope:** Daily log lifecycle, promotion workflow, context-hold expiry, regression capture template, maintenance SOP. This is the operational rhythm layer.

**Estimated complexity:** Medium. Mostly workflow documentation and lightweight tooling. The patterns exist but aren't codified.

<context>
Work order: WO-C2-003 — Daily Logs + Durable Memory Maintenance
Project: Clawrari Memory Architecture (FRD-C2)

Current daily log pattern:
- Files named memory/YYYY-MM-DD.md
- Created manually when the day's first notable event occurs
- Appended throughout the day with events, decisions, outcomes
- Read during startup: today + yesterday

Current maintenance (from HEARTBEAT.md):
- QMD freshness guard: reindex if stale >24h
- RLHF check: scan #prasclaw-briefings for corrections
- Agent registry: check for stale running agents
- No formal promotion workflow from daily → durable
- No formal context-hold expiry check
- Brief rewrite happens ad hoc at session end

Current durable files:
- regressions.md — has entries but no standardized capture template
- context-holds.md — has entries with expiry dates but no automated expiry detection
- predictions.md — has entries with outcome tracking
- All other durable files: projects, people, preferences, rules — maintained manually

What exists: The daily logging pattern, durable files, and HEARTBEAT maintenance.
What's missing: Daily log creation helper, promotion workflow, context-hold expiry detection, standardized regression template, formal maintenance SOP.
</context>

<constraints>
- Daily logs are append-only during the day. Do not overwrite earlier entries.
- Promotion from daily to durable is a deliberate act, not automatic in v1.
- Context-hold expiry detection should surface expired holds, not auto-delete them.
- Regression capture must include enough structure for guardrail derivation.
- Maintenance SOP must be runnable by both human operators and automated agents.
- Do not duplicate HEARTBEAT.md content — reference it and extend where needed.
</constraints>

<acceptance_criteria>
This work order is complete when:
- [ ] Daily log creation helper generates memory/YYYY-MM-DD.md with date header and section scaffolding
- [ ] Append workflow documents how to add entries (format, ordering, what qualifies as notable)
- [ ] Promotion rules define when and how daily content graduates to durable memory files
- [ ] Context-hold expiry check identifies holds past their expiry date/condition
- [ ] Expired holds are surfaced during startup or maintenance (not silently deleted)
- [ ] Regression capture template standardizes: Name, Date, Context, Failure Mode, Guardrail/Lesson, Status
- [ ] Promotion path from regression to constitutional/tactical rule is documented
- [ ] Maintenance SOP covers: brief rewrite cadence, daily log review, durable memory promotion, hold expiry, regression review, reindex trigger
- [ ] Maintenance SOP integrates with HEARTBEAT.md without duplicating it
</acceptance_criteria>

#### Implementation Steps

1. **Design daily log template and creation helper**

   Daily log file format (`memory/YYYY-MM-DD.md`):
   ```markdown
   # YYYY-MM-DD — [Day of Week]

   ## Key Decisions

   ## Actions Taken

   ## Events & Observations

   ## Carry Forward
   <!-- Items that didn't get resolved today — candidates for session brief -->
   ```

   Creation helper:
   - Generates the file with today's date if it doesn't exist
   - If it exists, does nothing (idempotent)
   - Can be called during startup or on first write of the day

2. **Document the append workflow**

   What qualifies as a notable daily log entry:
   - Decisions made (especially those with tradeoffs or rationale)
   - Tasks started, completed, or blocked
   - Errors encountered and how they were resolved
   - User instructions that change operating behavior
   - External events that affect priorities (meetings, messages, deployments)

   What does NOT belong in daily logs:
   - Routine file reads or searches (noise)
   - Duplicate entries for the same event
   - Full code diffs (reference the commit instead)

   Append format: Bullet points under the appropriate section. Include timestamps for time-sensitive items. Use consistent notation: `[HH:MM]` prefix for timed entries.

3. **Define promotion rules (daily → durable)**

   Promotion triggers:
   - A pattern appears in 3+ daily logs → candidate for durable memory
   - A decision is referenced in a later session → promote to relevant durable file
   - A person, project, or preference is mentioned repeatedly → promote to people/projects/preferences
   - A failure repeats → promote to regressions.md
   - User explicitly asks to remember something long-term

   Promotion workflow:
   1. Identify the candidate entry in the daily log
   2. Determine the target durable file (projects, people, preferences, regressions, rules, etc.)
   3. Write a clean entry in the target file following its format conventions
   4. Optionally mark the daily log entry as `[promoted → {target file}]`
   5. Do NOT delete the daily log entry — daily logs are archival

   Promotion is a deliberate act in v1. No auto-promotion. The assistant may suggest promotions; the operator decides.

4. **Implement context-hold expiry detection**

   Context-hold format (in `memory/context-holds.md`):
   ```markdown
   ### [Hold Name]
   - **Created:** YYYY-MM-DD
   - **Expires:** YYYY-MM-DD | [condition description]
   - **Instruction:** [What the assistant should do while this hold is active]
   ```

   Expiry detection:
   - Parse each hold's `Expires` field
   - If it's a date: compare to today. If past → mark as expired.
   - If it's a condition: surface it for human evaluation (cannot auto-resolve in v1).
   - Expired holds are moved to an `## Expired Holds` section at the bottom of the file.
   - Active and expiring-soon holds (within 48h) are surfaced in the session brief.

5. **Standardize regression capture**

   Regression entry format (in `memory/regressions.md`):
   ```markdown
   ### REG-[NNN]: [Regression Name]
   - **Date captured:** YYYY-MM-DD
   - **Context:** [What was happening when the failure occurred]
   - **Failure mode:** [What went wrong, specifically]
   - **Root cause:** [Why it happened, if known]
   - **Guardrail / Lesson:** [What should change to prevent recurrence]
   - **Status:** active | resolved | promoted-to-rule
   - **Rule linkage:** [If promoted: reference to constitutional or tactical rule]
   ```

   Promotion path from regression to rule:
   - If a regression is severe and universal → promote to `rules-constitutional.md`
   - If a regression is operational and context-dependent → promote to `rules-tactical.md`
   - When promoted, update the regression's Status to `promoted-to-rule` and add a Rule linkage reference

6. **Write the Maintenance SOP**

   The SOP defines the full maintenance cadence. It lives as a standalone document (e.g., `specs/maintenance-sop.md`) and is referenced from HEARTBEAT.md.

   | Activity | Trigger | Frequency | Owner |
   |----------|---------|-----------|-------|
   | Session brief rewrite | End of significant session, major state change | Per session (if warranted) | Agent |
   | Daily log creation | First notable event of the day | Daily | Agent |
   | Daily log append | Notable events throughout the day | Continuous | Agent |
   | Context-hold expiry check | Session startup, maintenance cycle | Daily | Agent |
   | Promotion review | End of week, or when patterns emerge | Weekly | Agent + Operator |
   | Regression review | Session startup (scan for active regressions) | Per session | Agent |
   | Durable file freshness | When content feels stale or contradictory | Ad hoc | Operator |
   | Semantic reindex | After meaningful memory writes, or when stale >24h | Event-driven + daily fallback | Agent |
   | MEMORY.md index sync | When core memory files are added or removed | On change | Agent |

   Integration with HEARTBEAT.md:
   - HEARTBEAT.md continues to own the QMD freshness guard and RLHF check
   - The Maintenance SOP adds: daily log creation check, context-hold expiry check, promotion review reminder
   - HEARTBEAT.md references the SOP for detailed procedures

#### Test Strategy

- Create a daily log for today → file created with correct date and scaffolding
- Create again → no overwrite, idempotent
- Append an entry → entry appears in correct section
- Set a context hold with yesterday's date → expiry check identifies it as expired
- Set a context hold with tomorrow's date → not flagged as expired
- Create a regression entry using the template → all fields present and parseable
- Run maintenance SOP checklist → all activities can be performed without errors
- Verify promotion workflow: create a daily entry, promote to projects.md, verify both files updated

---

### WO-C2-004: Local Semantic Retrieval Layer

**Objective:** Formalize the semantic retrieval system (QMD) into a documented, reproducible layer that indexes human-readable memory files, supports ranked search with source metadata, provides a search-then-read workflow contract, and includes reindex/freshness management.

**Scope:** Chunking/indexing pipeline spec, SQLite metadata store schema, retrieval API contract, search-then-read workflow documentation, reindex/freshness spec. This formalizes the existing QMD integration.

**Estimated complexity:** High. The QMD tool exists but the indexing pipeline, API contract, and freshness management need formal specification. This is the most technically involved work order.

<context>
Work order: WO-C2-004 — Local Semantic Retrieval Layer
Project: Clawrari Memory Architecture (FRD-C2)

Current QMD state:
- QMD (Quantized Memory Documents) is the existing semantic search tool
- scripts/qmd-reindex.sh triggers reindexing
- HEARTBEAT.md checks QMD freshness: if "Updated" is >24h ago, reindex immediately
- memory/heartbeat-state.json tracks last reindex timestamp
- MEMORY.md references memory_search and memory_get as the retrieval commands
- memory/reference/conventions.md documents type-tag + trust score conventions

Current retrieval pattern (informal):
1. Use memory_search to find relevant file + section
2. Use memory_get to pull specific lines
3. Use daily files for recent context
4. Use MEMORY.md index for orientation

What exists: QMD tool, reindex script, freshness check in HEARTBEAT.md.
What's missing: Formal chunking spec, SQLite schema documentation, retrieval API contract, search-then-read workflow spec, freshness detection criteria, indexing pipeline documentation.
</context>

<constraints>
- Semantic layer augments plain-text files — never replaces them.
- No cloud-only dependencies. Local embedding and local SQLite only.
- Retrieval must return source file path and line/section metadata (not just text).
- If the semantic index breaks, the human-readable memory files must still be usable.
- Reindex must be safe to run at any time (idempotent, non-destructive).
- Do not change the existing QMD tool behavior — document and extend its integration.
</constraints>

<acceptance_criteria>
This work order is complete when:
- [ ] Chunking spec defines how memory files are split into indexable units (chunk size, overlap, boundaries)
- [ ] SQLite metadata store schema is documented (tables, fields, relationships)
- [ ] Indexing pipeline spec covers: which files are indexed, chunking, embedding generation, metadata storage
- [ ] Retrieval API contract defines: input (query string), output (ranked results with path, line range, snippet, score)
- [ ] Search-then-read workflow is documented as an operational norm with examples
- [ ] Reindex command is specified: trigger conditions, behavior, expected output
- [ ] Freshness detection criteria are formalized: how to detect stale index, threshold, resolution
- [ ] Graceful degradation: if index is unavailable, fallback to file-based search is documented
- [ ] All indexed content traces back to human-readable source files
</acceptance_criteria>

#### Implementation Steps

1. **Specify the indexing pipeline**

   Which files are indexed:
   - All files in `memory/` (except `heartbeat-state.json`)
   - `MEMORY.md` (the index itself, for meta-retrieval)
   - Daily logs from the last 30 days (older logs can be indexed on demand)
   - NOT indexed: SOUL.md, USER.md, AGENTS.md (these are loaded directly at startup)

   Chunking strategy:
   - Primary boundary: Markdown headers (`##`, `###`)
   - Secondary boundary: Blank-line-separated paragraphs within a section
   - Target chunk size: 200-500 tokens (tunable)
   - Overlap: 1-2 sentences between adjacent chunks for context continuity
   - Each chunk retains metadata: source file path, line range (start-end), section header hierarchy, last-modified timestamp

   Embedding generation:
   - Local embedding model (document the recommended model and version)
   - Batch processing: index all chunks at once during reindex
   - Incremental option: index only chunks from files modified since last reindex (stretch goal for v1)

2. **Document the SQLite metadata store schema**

   ```sql
   -- Core tables
   CREATE TABLE chunks (
     id INTEGER PRIMARY KEY,
     file_path TEXT NOT NULL,
     line_start INTEGER NOT NULL,
     line_end INTEGER NOT NULL,
     section_header TEXT,           -- e.g., "## Active Context > ### FRD-5 Status"
     content TEXT NOT NULL,
     token_count INTEGER,
     created_at TEXT NOT NULL,      -- ISO 8601
     file_modified_at TEXT NOT NULL -- ISO 8601, for freshness checks
   );

   CREATE TABLE embeddings (
     chunk_id INTEGER PRIMARY KEY REFERENCES chunks(id),
     embedding BLOB NOT NULL,       -- serialized float vector
     model_id TEXT NOT NULL          -- embedding model identifier
   );

   CREATE TABLE index_metadata (
     key TEXT PRIMARY KEY,
     value TEXT NOT NULL
   );
   -- Keys: last_reindex_at, total_chunks, model_id, index_version
   ```

   Notes:
   - Embedding storage uses SQLite BLOB for portability
   - `file_modified_at` enables per-file freshness checks
   - `index_metadata` tracks global index state

3. **Define the retrieval API contract**

   **Search operation** (`memory_search` or equivalent):
   - Input: query string (natural language), optional filters (file path glob, date range, section header)
   - Output: ranked list of results, each containing:
     - `file_path`: source file
     - `line_start`, `line_end`: exact line range in source file
     - `section_header`: section context
     - `snippet`: the matched text (truncated to ~500 chars)
     - `score`: relevance score (0.0 to 1.0)
   - Default: return top 5 results
   - Results are ordered by score descending

   **Get operation** (`memory_get` or equivalent):
   - Input: file path, line range (start, end) or section header
   - Output: the exact text from the source file at the specified location
   - This is a direct file read — no semantic processing

   **Contract guarantees:**
   - Every snippet in search results corresponds to actual content in the source file at the given line range
   - If the source file has changed since indexing, the result includes a `stale: true` flag
   - Search never modifies any file

4. **Document the search-then-read workflow**

   The canonical retrieval pattern for assistants:

   ```
   Step 1: SEARCH — Use memory_search with a natural language query
           → Receive ranked results with file paths and line ranges

   Step 2: EVALUATE — Review the returned snippets and scores
           → Decide which results are relevant (score threshold: 0.5 suggested)
           → If no results meet threshold, consider broadening the query

   Step 3: READ — Use memory_get to read the full context around the best match
           → Read the specific line range, plus surrounding context if needed
           → Do NOT open the entire file unless the result spans most of it

   Step 4: ACT — Use the retrieved information to inform the current task
           → Cite the source file and section when referencing retrieved memory
   ```

   Anti-patterns to avoid:
   - Opening entire memory files and scanning manually (defeats the purpose)
   - Searching and then ignoring results in favor of full-file reads
   - Searching for something you already know the location of (just read it directly)

5. **Specify the reindex workflow**

   Trigger conditions:
   - **Time-based:** QMD freshness guard triggers when last reindex >24h ago
   - **Event-based:** After significant memory writes (brief rewrite, durable file update, promotion)
   - **Manual:** Operator or agent runs reindex command explicitly

   Reindex behavior:
   1. Scan all indexable files (per indexing pipeline spec)
   2. Compare file `modified_at` timestamps against stored `file_modified_at` in chunks table
   3. Re-chunk and re-embed files that have changed
   4. Remove chunks for files that no longer exist
   5. Update `index_metadata.last_reindex_at`
   6. Report: files reindexed, chunks added/updated/removed, total index size

   Freshness detection:
   - Check `index_metadata.last_reindex_at` against current time
   - For each file: compare disk `modified_at` against stored `file_modified_at`
   - Freshness states:
     - **FRESH:** last reindex <24h ago AND no files modified since
     - **STALE:** last reindex >24h ago OR files modified since last reindex
     - **BROKEN:** index database missing or corrupted
   - STALE triggers automatic reindex (per HEARTBEAT.md)
   - BROKEN triggers alert to operator

6. **Document graceful degradation**

   If the semantic index is unavailable (broken, missing, or being rebuilt):
   - Fall back to file-based search: use grep/ripgrep over memory files
   - Use MEMORY.md index to identify the most likely file for a given query topic
   - Read relevant files directly, starting with the most likely candidates
   - Log the fallback event so it can be investigated

   The system must never fail silently because the index is broken. The human-readable files are always the source of truth.

#### Test Strategy

- Index a fresh memory workspace → all files chunked, embeddings generated, metadata stored
- Search for a known topic (e.g., "project status") → relevant chunk returned with correct file path and line range
- Verify returned line range matches actual file content → read the file at those lines, confirm match
- Modify a memory file → freshness check reports STALE
- Reindex after modification → freshness returns to FRESH, modified chunks updated
- Delete the index database → system falls back to file-based search with warning
- Search with no relevant results → empty result set, no crash
- Index older daily logs → only last 30 days indexed by default
- Verify search-then-read workflow end-to-end: search → evaluate → read → information matches

---

## Progress

- [x] (2026-04-10) Read all input files: FRD, prep doc, audit, ExecPlan template, live workspace files
- [x] (2026-04-10) Analyzed current state vs needed state
- [x] (2026-04-10) Drafted all 4 work orders with 8090 format
- [x] (2026-04-10) Built requirements traceability matrix
- [ ] Review pass by Prasith
- [ ] Work order refinement based on review
- [ ] Implementation begins

## Context and Orientation

This blueprint covers FRD-C2, the Memory Architecture feature of Clawrari. It is the second FRD but the first to be blueprinted because memory is the substrate layer that FRD-C3 (Self-Improvement) and all subsequent features depend on.

The critical insight is that **nearly everything described in the FRD already exists** in Prasith's live workspace. The session brief, subagent ledger, daily logs, durable memory files, startup sequence, QMD search, and maintenance routines are all running in production. What's missing is the packaging: seed templates, bootstrap tooling, validation, formal specs, and documentation that let someone reproduce the system from scratch.

This blueprint therefore focuses on **formalization and reproducibility**, not invention. Where the live system works, we document it. Where it relies on implicit conventions, we make those conventions explicit and machine-checkable.

The four work orders are sequenced from infrastructure (file structure) to behavior (startup, maintenance, retrieval), matching the natural dependency chain.

## Surprises & Discoveries

- The live workspace already satisfies the spirit of all 12 requirements. The gap is entirely in formalization and packaging.
- The session brief's 50-line budget is a stated convention but has no enforcement mechanism — the current brief is 66 lines (over budget).
- The context-hold expiry system exists in concept but has no automated detection.
- The QMD freshness guard in HEARTBEAT.md is the closest thing to a formal spec for the retrieval layer, but it covers only the reindex trigger, not the full pipeline.

## Decision Log

- **Decision:** influences.md excluded from v1 seed templates.
  Rationale: Setup audit marked it as "too personal." Can be added as an optional extension.
  Date: 2026-04-10

- **Decision:** Daily logs from last 30 days indexed; older on demand.
  Rationale: Keeps index size manageable. Most retrieval targets recent context. Older context is still accessible via direct file read.
  Date: 2026-04-10

- **Decision:** Promotion from daily to durable is manual in v1 (agent suggests, operator decides).
  Rationale: Auto-promotion risks polluting durable memory with noise. The FRD's open questions flagged this. Manual promotion preserves quality.
  Date: 2026-04-10

- **Decision:** Session-type default is "main session" when detection is ambiguous.
  Rationale: Loading more context (safe) is better than loading less (risky). Main session loads MEMORY.md; subagent sessions skip it.
  Date: 2026-04-10

- **Decision:** Context-hold expiry detection surfaces holds but doesn't auto-delete.
  Rationale: Expired holds may need human review before removal. Auto-delete risks losing context the operator still wants.
  Date: 2026-04-10

- **Decision:** Startup spec allows graceful degradation (warnings, not crashes) for missing files.
  Rationale: A partially loaded session is better than no session. Missing files should be flagged for repair, not block work.
  Date: 2026-04-10

## Outcomes & Retrospective

_To be completed after implementation._

---

## Rules (Non-Negotiable)

1. **Self-contained.** A novice can implement end-to-end from ONLY this document.
2. **Living.** Update as progress is made and discoveries occur. Every revision stays self-contained.
3. **Observable outcomes.** Define acceptance as behavior, not code changes.
4. **Idempotent and safe.** Steps can be run multiple times. Include fallbacks for destructive operations.
5. **Validation is not optional.** Include test commands, expected outputs, and proof of success.
6. **No external references.** If knowledge is required, embed it. Don't point to blogs or docs.
7. **XML sections are agent interfaces.** `<context>`, `<constraints>`, `<acceptance_criteria>` must be kept complete and contradiction-free. Agents parse these directly — ambiguity wastes reasoning tokens.
