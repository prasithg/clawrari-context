# FRD-C2: Memory Architecture

## Summary

Clawrari Memory Architecture is the structured memory layer for an OpenClaw-based assistant operating system. Its job is to give an assistant durable context without turning every session into a blind restart or an unbounded context dump. The system combines fast-start session memory, durable human-readable records, explicit rule layers, and semantic retrieval so the assistant can resume work, preserve decisions, learn from regressions, and coordinate subagents with minimal operator effort.

The core product promise is simple: an assistant should feel continuous across sessions, but auditable, editable, and predictable. Memory must be inspectable in files, recoverable after failures, cheap to maintain, and structured enough for tools to retrieve the right fact at the right time. This feature packages a proven internal operating pattern into an installable default for serious assistant users, especially builders, operators, and founders who want an assistant that improves over time instead of forgetting everything between chats.

This FRD defines the memory system as a first-class feature of Clawrari. It covers memory file layout, startup loading order, recency capture, durable knowledge categories, semantic indexing and retrieval, subagent state tracking, regression capture, context holds, and memory maintenance workflows.

### In scope
- Standard memory directory structure and file taxonomy
- Deterministic session startup sequence
- Session brief as a bounded, high-signal preconscious buffer
- Daily logs for recent raw activity capture
- Durable thematic memory files for projects, people, preferences, rules, regressions, and predictions
- Subagent ledger for spawned-task tracking and stale-task detection
- Semantic indexing over memory documents for retrieval
- Targeted retrieval workflow using search first, read second
- Reindex and freshness workflow for semantic memory
- Rules for rewriting summaries and maintaining memory health
- Human inspectability and direct editability of memory files

### Out of scope
- Fully autonomous memory writing without any policy constraints
- Hidden proprietary vector services or required cloud dependency
- Cross-user shared memory syncing across multiple operators
- Rich GUI memory management console in v1
- Automatic truth verification of every stored claim
- Full personal knowledge management replacement beyond assistant operations
- General long-term world knowledge storage unrelated to the assistant’s user or work

## User Entry Point

A user installs Clawrari and enables the Memory Architecture module as part of workspace bootstrap. The module creates a standard memory folder, seed files, indexing database, and maintenance scripts. From that point forward, every assistant session starts by loading a compact session brief, checking subagent continuity, reading recent daily logs, and consulting the long-term memory index when needed.

The user experiences this as continuity. The assistant remembers active projects, recent commitments, known issues, stable preferences, and prior decisions without requiring the user to re-explain them every session.

## Terminology

- **Session brief**: A tightly bounded, high-signal summary loaded first at session start. It acts as the assistant’s preconscious state.
- **Daily log**: A date-named log file that records raw activity, decisions, outcomes, and relevant events for that day.
- **Durable memory file**: A long-lived file dedicated to a thematic category such as projects, people, preferences, rules, regressions, or predictions.
- **Memory index**: A top-level map explaining the purpose and location of memory files.
- **Subagent ledger**: An append-only record of delegated tasks and their completion or staleness state.
- **Context hold**: A temporary instruction, priority, or filter with an expiry condition.
- **Regression**: A named failure pattern that should be turned into a guardrail, rule, or workflow improvement.
- **Constitutional rules**: High-stability rules that should rarely change.
- **Tactical rules**: Lower-stability operating conventions that evolve with usage.
- **QMD (Quantized Memory Documents)**: The semantic retrieval layer that indexes memory content into embeddings plus SQLite-backed metadata for search.
- **Memory freshness**: The degree to which indexed semantic memory reflects the current file state.

## User Journey

A new operator installs Clawrari for an assistant workspace. The memory module scaffolds a standard set of files and documents their purpose in a memory index. During setup, the operator can optionally seed projects, people, preferences, and rule files.

At the start of each session, the assistant reads the session brief first so it immediately absorbs active priorities, hot tasks, recent decisions, expiring holds, and known issues. It then checks the subagent ledger for unfinished or stale work, reads today’s and yesterday’s daily logs for recency, and, in primary sessions, reads the broader memory index to orient itself to the full memory system.

As work proceeds, the assistant uses semantic search when it needs prior decisions, relationship context, preferences, or historical outcomes. Search returns candidate snippets from memory documents. The assistant then performs targeted reads rather than opening whole files, keeping retrieval efficient and explainable.

At the end of a meaningful work block, the assistant writes or updates the daily log, refreshes the session brief with the highest-signal carry-forward context, and records any new regressions, predictions, or durable lessons in the proper long-term files. If subagents were spawned, the ledger reflects their lifecycle.

Over time, the system becomes more useful because it preserves not only facts, but operating patterns: what matters now, what changed recently, what should never be forgotten, what failed before, and what assumptions need to be tested.

## Requirements

### REQ-MEM-001: Standard Memory Workspace Structure
**Experience goal:** Users should get a memory system that works out of the box, with clear places for different kinds of knowledge.

**User story:** As an operator, I want Clawrari to create a standard memory layout so the assistant’s context is durable, organized, and human-editable.

**Acceptance criteria:**
- The system creates a default memory directory and seed files during installation or bootstrap.
- The structure includes session brief, subagent ledger, daily logs, durable memory files, reference conventions, heartbeat state, and memory index.
- File names and locations are documented in a top-level index.
- The structure can be regenerated safely when missing files are detected.

**Technical enablers:**
- Bootstrap scaffolding script
- Seed templates for core files
- Memory index template
- Workspace validation routine

### REQ-MEM-002: Deterministic Session Startup Sequence
**Experience goal:** The assistant should start every session with the right context in the right order, instead of depending on ad hoc prompts.

**User story:** As a user, I want a consistent startup sequence so the assistant reliably loads urgent and recent context before broader archival memory.

**Acceptance criteria:**
- The startup sequence always reads the session brief first.
- The system checks the subagent ledger for incomplete or stale entries before broader work begins.
- The system reads the current and previous daily logs during session startup.
- Main sessions additionally load the top-level memory index.
- The startup sequence is documented and machine-checkable.

**Technical enablers:**
- Startup workflow spec
- Session-type detection
- Ordered read hooks
- Validation tests for startup order

### REQ-MEM-003: Bounded Session Brief
**Experience goal:** The assistant should begin with a sharp, compact understanding of what matters now.

**User story:** As an operator, I want the assistant to load a small high-signal buffer first so it can resume useful work quickly without reading everything.

**Acceptance criteria:**
- The session brief has an enforced size or line budget.
- The brief is optimized for active context, hot tasks, recent decisions, expiring holds, and known issues.
- The brief is rewritten after significant sessions or major state changes.
- The brief favors current relevance over archival completeness.
- The brief remains human-readable and directly editable.

**Technical enablers:**
- Brief template and rewrite rubric
- Size-budget validator
- Session-end maintenance workflow
- Optional summarization helper

### REQ-MEM-004: Daily Log Capture
**Experience goal:** Recent activity should be preserved in raw form before it is distilled into durable memory.

**User story:** As a user, I want daily logs so the assistant can recover recency, chronology, and context that may not yet belong in long-term files.

**Acceptance criteria:**
- Daily logs are created using a date-based naming convention.
- The assistant can append notable events, decisions, actions, and outcomes throughout the day.
- Startup reads include today and yesterday by default.
- Daily logs can act as the source material for later promotion into durable memory.

**Technical enablers:**
- Date-based file generator
- Append-safe logging pattern
- Promotion guidance from daily to durable memory
- Read helpers for recent files

### REQ-MEM-005: Durable Thematic Memory Files
**Experience goal:** Stable knowledge should live in the right place so the assistant can retain patterns, not just transcripts.

**User story:** As an operator, I want separate long-term memory files for different knowledge types so edits are understandable and retrieval quality improves.

**Acceptance criteria:**
- The system supports durable files for at least projects, people, preferences, regressions, constitutional rules, tactical rules, context holds, predictions, and influences.
- Each file has a documented purpose and guidance for what belongs there.
- Durable files are designed for incremental maintenance rather than full rewrites.
- Users can add categories without breaking retrieval.

**Technical enablers:**
- File taxonomy spec
- Seed headers and examples
- Category metadata or conventions
- Extensible indexing rules

### REQ-MEM-006: Memory Index and Discoverability
**Experience goal:** Users and assistants should be able to understand the memory system without reverse-engineering it.

**User story:** As a user, I want a memory index that explains the memory architecture so I can inspect, trust, and maintain it.

**Acceptance criteria:**
- A top-level memory index explains each core file and its purpose.
- The index distinguishes recent-context files from long-term files and operational state.
- The index is updated when new core memory files are introduced.
- The assistant can use the index to decide where information should be written.

**Technical enablers:**
- MEMORY.md template
- Change management checklist for memory additions
- Lint rule for undocumented core files

### REQ-MEM-007: Semantic Retrieval Layer
**Experience goal:** The assistant should be able to find relevant prior knowledge without brute-force reading the entire memory store.

**User story:** As an operator, I want semantic search over memory so the assistant can retrieve the right historical context quickly and precisely.

**Acceptance criteria:**
- Memory documents are indexed into a local semantic retrieval system.
- Search returns ranked candidate snippets with source path and location metadata.
- The retrieval layer works against human-readable memory files rather than a hidden-only store.
- The system supports reindexing when files change.
- Retrieval remains functional without requiring a cloud-only dependency.

**Technical enablers:**
- SQLite-backed metadata store
- Embedding generation pipeline
- QMD document chunking and indexing
- Reindex script and freshness checks

### REQ-MEM-008: Search-Then-Read Retrieval Workflow
**Experience goal:** Memory access should be precise, efficient, and explainable.

**User story:** As a user, I want the assistant to search memory first and then read only the relevant sections so context use stays efficient and auditable.

**Acceptance criteria:**
- The documented retrieval pattern is semantic search first, targeted read second.
- Search results include enough metadata to support pinpoint reads.
- The assistant avoids opening large files in full when targeted retrieval is sufficient.
- Retrieval behavior is documented as an operational norm.

**Technical enablers:**
- Search API contract
- Line-addressable file access
- Retrieval policy documentation
- Optional observability logs for memory access patterns

### REQ-MEM-009: Subagent Continuity Ledger
**Experience goal:** Delegated work should not disappear into a void.

**User story:** As an operator, I want spawned tasks tracked in an append-only ledger so the assistant can resume, audit, and detect stale delegated work.

**Acceptance criteria:**
- The system records subagent spawn events and completion events in a dedicated ledger.
- Startup checks identify entries that appear stale based on configurable thresholds.
- Ledger records are append-only by default for auditability.
- The assistant can surface stale delegated work in the session brief or startup notes.

**Technical enablers:**
- Ledger schema
- Append utilities
- Staleness detection rules
- Startup stale-check hook

### REQ-MEM-010: Regression Capture and Guardrail Learning
**Experience goal:** The assistant should get harder to break over time.

**User story:** As a user, I want repeat failures captured as named regressions so the assistant can convert mistakes into durable safeguards.

**Acceptance criteria:**
- The system provides a dedicated regression log or file.
- Regressions are recorded with a name, context, failure mode, and intended guardrail or lesson.
- Regressions are part of regular memory review during sessions.
- Users can distinguish one-off mistakes from recurring patterns worth encoding.

**Technical enablers:**
- Regression template
- Promotion criteria from incident to regression
- Guardrail linkage to tactical or constitutional rules
- Review checklist

### REQ-MEM-011: Context Holds with Expiry
**Experience goal:** Temporary instructions should persist while they matter, then fall away cleanly.

**User story:** As an operator, I want time-bounded context holds so the assistant remembers temporary priorities and filters without treating them as permanent truth.

**Acceptance criteria:**
- The system supports storing temporary context holds with expiry conditions or dates.
- Expired holds can be identified during startup or maintenance.
- Holds are kept distinct from durable preferences or rules.
- The session brief can surface active and expiring holds.

**Technical enablers:**
- Context-hold schema
- Expiry parser or validator
- Startup maintenance check
- Brief inclusion logic

### REQ-MEM-012: Memory Maintenance and Freshness Workflow
**Experience goal:** Memory quality should stay high without requiring obsessive manual upkeep.

**User story:** As a user, I want lightweight maintenance workflows so summaries, indexes, and semantic retrieval stay fresh as memory evolves.

**Acceptance criteria:**
- The system defines when to rewrite the session brief, when to append daily logs, and when to promote content into durable memory.
- The system provides a reindex workflow after meaningful memory changes.
- Freshness failures in semantic memory can be detected and corrected.
- Maintenance routines are documented and runnable by operators.

**Technical enablers:**
- Maintenance SOP
- Reindex script
- Freshness timestamps or checksums
- Optional validation command

## Implementation Notes

The memory architecture should ship as a local-first, file-first system. That is the right call. It keeps the system legible, versionable, and robust under failure. The semantic layer should augment files, not replace them. If the index breaks, the human-readable memory still exists. If a user wants to inspect or edit a belief, preference, or rule, they should do so in plain text.

Clawrari should treat memory as operational infrastructure, not decoration. The quality of session continuity depends less on model cleverness than on disciplined memory boundaries, recency handling, and retrieval hygiene. The combination of a bounded session brief, recent daily logs, durable thematic files, and semantic search is the heart of the design.

## Success Metrics

- Time to useful session resumption decreases after installation.
- Users report less need to restate active projects, preferences, and recent decisions.
- Memory retrieval returns relevant snippets with high enough precision to support targeted reads.
- Regression capture results in observable guardrail improvements over time.
- Stale subagent work is surfaced before it silently disappears.
- Operators can inspect and manually edit memory without specialized tooling.

## Open Questions

- Should v1 include optional auto-suggestions for promoting daily log content into durable memory files?
- How opinionated should the default schemas be versus allowing freeform markdown from the start?
- Should the freshness workflow run automatically after writes, or remain explicitly operator-invoked in v1?
- What is the minimum viable observability needed to debug poor retrieval quality without adding clutter?
