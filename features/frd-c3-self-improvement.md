# FRD-C3: Self-Improvement Framework

**Status:** Draft
**Owner:** Prasith + Claw
**Date:** April 8, 2026

---

## Summary

The Self-Improvement Framework is Clawrari's system for making an AI assistant measurably better over time without requiring the user to manually retrain prompts, rewrite files, or babysit the assistant's failures. It turns everyday usage into structured feedback loops.

Today, most AI assistants are stateless in practice. They may remember some facts, but they do not reliably learn from repeated mistakes, user corrections, operational friction, or forecast misses. The result is familiar: the same bugs recur, good advice is not promoted into durable behavior, and the assistant's confidence does not get better calibrated.

Clawrari's promise is stronger: your assistant should improve through use. That means it must capture failures, convert feedback into behavior changes, measure prediction quality, identify recurring friction, benchmark possible improvements, and promote durable learnings into the operating system layer.

This FRD defines a generalizable framework made of five connected loops:
1. **Error prevention and recovery**: log failures, regressions, and retries so the same mistake is less likely to happen twice.
2. **Human feedback incorporation**: ingest user corrections and convert them into durable operating guidance.
3. **Learning capture and promotion**: record insights at the point of work, then promote the best ones into long-lived memory or system rules.
4. **Performance evaluation and optimization**: benchmark prompts, skills, and workflows before adopting changes.
5. **Calibration and friction analysis**: track confidence against outcomes and identify where the assistant creates drag.

Together, these loops form the backbone of Clawrari's claim that it is not just configurable, but self-improving.

### In Scope
- A standardized data model and file structure for improvement signals
- Built-in workflows for collecting, reviewing, promoting, and applying learnings
- Session- and heartbeat-level checks that surface outstanding improvement work
- Bench and review loops for evaluating candidate changes before promotion
- Generalizable templates that work across work and personal personas

### Out of Scope
- Autonomous self-editing of arbitrary source code without review
- Model fine-tuning or weight updates
- Fully automatic acceptance of every detected improvement
- Organization-wide analytics dashboards for multi-user deployments
- Guarantees of improvement without any user interaction at all

---

## User Entry Point

A user experiences this feature in three places:

1. **During normal use**: the assistant captures failures, friction, predictions, and feedback in the background.
2. **During session startup and heartbeat checks**: the assistant loads active regressions, pending failures, and high-value learnings before work continues.
3. **During review moments**: the user or maintainer can inspect what the system learned, what changed, what is still unresolved, and which improvements should be promoted.

The primary entry point is not a dashboard. It is the assistant itself behaving better because the framework is active.

---

## Terminology

- **Improvement signal**: Any structured evidence that the assistant should change behavior. Examples: failures, user corrections, friction notes, prediction outcomes, benchmark results.
- **Regression**: A named, durable record of a mistake important enough to guard against in future sessions.
- **Failure log**: A structured record of an execution that did not complete as intended, including retry state and resolution status.
- **Learning**: An insight, pattern, or operating rule captured from experience.
- **Promotion**: Moving a learning from a temporary or local record into a durable, reusable system artifact.
- **Calibration**: The match between the assistant's confidence and actual outcomes.
- **Friction**: Repeated drag in the user experience, even if the task technically succeeds.
- **Improvement candidate**: A possible change to prompts, skills, workflows, or defaults that should be tested before adoption.
- **Heartbeat review**: A recurring operational review where outstanding failures, stale retries, and pending improvements are surfaced.

---

## User Journey

A typical journey starts with normal work, not configuration.

The user asks the assistant to do something real. If the assistant fumbles, the issue is captured as either an operational failure, a regression, or a friction note depending on severity and repeatability. If the user corrects the assistant, that feedback is logged as an improvement signal and acknowledged.

Later in the same day or week, the assistant reviews collected signals. Repeated mistakes are promoted into named guardrails. Useful patterns discovered during work are stored as candidate learnings. If a change seems promising, such as a revised prompt or workflow, the assistant benchmarks it against representative cases before adopting it.

Over time, predictions are checked against actual outcomes, so the assistant becomes less overconfident and more trustworthy when speaking in probabilities. At the same time, recurring friction is surfaced even when tasks technically complete, preventing the system from optimizing only for task completion while ignoring usability.

When the next session begins, the assistant loads the relevant durable guardrails and learnings. The user does not need to repeat the same correction. The assistant starts from a better operating baseline.

The success condition is simple: after weeks of use, the assistant repeats fewer mistakes, incorporates corrections faster, makes more grounded predictions, and feels easier to work with.

---

## Requirements

### REQ-SI-001: Structured Improvement Signal Capture

**Title:** Capture improvement signals in a unified, structured way

**Experience Goal:** Users should not have to remember to manually document every lesson. When meaningful signals occur, the system should make capture lightweight and reliable.

**User Story:** As a user, when the assistant fails, gets corrected, causes friction, or makes a prediction that later resolves, I want that signal captured in a structured format so the system can improve from it instead of forgetting it.

**Acceptance Criteria:**
- The framework must define a standard schema for at least these signal types: failures, regressions, feedback, learnings, friction, predictions, and benchmark results.
- Each signal record must include a timestamp, category, summary, and enough context to support later review.
- The framework must support both manual capture and assistant-assisted capture during active work.
- The framework must separate temporary/local notes from durable/promoted records.
- The system must make it possible to link related records, such as a failure that later becomes a regression or a learning promoted from a friction review.

**Technical Enablers:**
- Canonical file and folder conventions for improvement artifacts
- Lightweight CLI or script helpers for logging signals
- Shared schema definitions and validation rules
- Cross-linking conventions or IDs for related records

### REQ-SI-002: Failure and Regression Prevention Loop

**Title:** Convert repeated mistakes into durable guardrails

**Experience Goal:** Once the assistant makes an important mistake and the root cause becomes clear, the same failure should become materially less likely in future sessions.

**User Story:** As a user, when the assistant repeats a mistake or hits a serious operational failure, I want that issue tracked, classified, and surfaced on future sessions so prior pain translates into future safety.

**Acceptance Criteria:**
- The framework must distinguish between one-off failures, unresolved failures needing retry, and durable regressions that should influence future behavior.
- Regression records must support at least: what happened, impact, root cause, and guardrail.
- Session startup or equivalent initialization must load active regressions before normal work proceeds.
- Heartbeat or recurring review workflows must surface unresolved failures and stale retry items.
- The framework must support status transitions for failures, including open, retried, resolved, and escalated states.
- The system must support promoting a repeated or severe failure into a named regression without duplicating all context manually.

**Technical Enablers:**
- Regression registry and failure tracker with stable IDs
- Startup hooks to load high-priority regressions
- Review scripts or checks for stale open failures
- Promotion workflow from failure records to regression records

### REQ-SI-003: Human Feedback Assimilation Loop

**Title:** Turn user corrections into durable behavior change

**Experience Goal:** When the user corrects the assistant, the assistant should acknowledge it, adapt, and avoid needing the same correction repeatedly.

**User Story:** As a user, when I tell the assistant it misunderstood my preference, tone, workflow, or decision rule, I want that correction to be incorporated into future behavior instead of treated as a one-off chat message.

**Acceptance Criteria:**
- The framework must support capturing explicit user feedback from designated channels or review surfaces.
- Feedback records must distinguish between style preferences, operational rules, factual corrections, and unresolved questions.
- The system must support acknowledgement states, such as seen, actioned, and needs clarification.
- The framework must define how accepted feedback becomes durable instruction, memory, or configuration.
- The system must preserve human review for ambiguous or high-impact feedback before promotion.
- It must be possible to trace a promoted behavior rule back to the originating feedback signal.

**Technical Enablers:**
- Feedback ingestion pattern from message threads, notes, or files
- Acknowledgement markers and timestamps
- Promotion path from feedback to persistent guidance artifacts
- Review queue for ambiguous or conflicting feedback

### REQ-SI-004: Learning Capture and Promotion System

**Title:** Preserve useful discoveries and promote the best ones

**Experience Goal:** Valuable lessons found during daily operations should not vanish in chat history or temporary notes.

**User Story:** As a maintainer, when the assistant or I discover a better pattern, workaround, or operating principle, I want to capture it quickly and later promote it into a reusable, durable asset.

**Acceptance Criteria:**
- The framework must support quick logging of candidate learnings during active work.
- The framework must support querying prior learnings so useful patterns can be reused.
- The system must distinguish unreviewed learnings from promoted learnings.
- Promotion must target durable artifacts such as memory files, rules, templates, playbooks, or system defaults.
- The framework must allow maintainers to record why a learning was promoted, not just the final rule.
- Duplicate or conflicting learnings must be reviewable before promotion.

**Technical Enablers:**
- Learning log and searchable store
- Promotion helpers for moving learnings into durable system files
- Metadata for review status, source, and promotion rationale
- Deduplication or conflict-review support

### REQ-SI-005: Evaluation and Auto-Optimization Loop

**Title:** Benchmark candidate improvements before adopting them

**Experience Goal:** The system should not treat every new idea as an improvement. Changes should be tested against representative tasks before becoming defaults.

**User Story:** As a maintainer, when I propose a new prompt, skill behavior, workflow, or guardrail, I want to benchmark it against prior behavior so only validated improvements are promoted.

**Acceptance Criteria:**
- The framework must support defining benchmark cases for prompts, skills, workflows, or decision policies.
- The system must support comparing a baseline and a candidate on the same task set.
- Benchmark results must be stored as improvement signals with timestamps and summary outcomes.
- The framework must support iterative improvement loops with bounded retries rather than endless optimization.
- Promotion of a candidate change must be tied to observable results or an explicit human override.
- The framework must support both automated evaluation signals and human qualitative review where objective scoring is insufficient.

**Technical Enablers:**
- Benchmark runner and case format
- Storage for baseline vs candidate results
- Iteration policy with stop conditions
- Promotion workflow gated on benchmark output or review decision

### REQ-SI-006: Prediction Calibration and Friction Review

**Title:** Improve trust by measuring confidence and surfacing drag

**Experience Goal:** The assistant should become more trustworthy over time, not just more active. That requires learning where it is overconfident and where it creates repeated user friction.

**User Story:** As a user, when the assistant makes predictions or recommendations with confidence, I want those claims checked against outcomes over time, and I want recurring friction surfaced even when tasks technically succeed.

**Acceptance Criteria:**
- The framework must support logging predictions with explicit confidence levels and later attaching observed outcomes.
- The system must support periodic review of calibration patterns such as overconfidence, underconfidence, or missing confidence statements.
- The framework must support logging friction signals independently of outright failures.
- Friction review must surface repeated pain points that merit workflow or product changes.
- The system must make calibration and friction findings available for future improvement planning and promotion decisions.
- The framework must support summary metrics or review outputs without requiring a complex external dashboard.

**Technical Enablers:**
- Prediction log format with confidence and outcome fields
- Metrics or summary-generation scripts
- Friction log and aggregation conventions
- Review routines that connect findings to learnings, regressions, or optimization candidates

### REQ-SI-007: Review, Governance, and Explainability Layer

**Title:** Make self-improvement auditable, reviewable, and safe

**Experience Goal:** Users should be able to trust the framework because they can inspect what changed, why it changed, and what still requires attention.

**User Story:** As a user or maintainer, I want a clear review layer over the assistant's self-improvement activity so I can understand changes, audit risky promotions, and keep the system aligned with my preferences.

**Acceptance Criteria:**
- The framework must provide a periodic review surface summarizing recent failures, promotions, pending signals, stale items, and benchmark outcomes.
- Every promoted change must record its source signals and promotion rationale.
- The framework must define which classes of changes can be auto-promoted and which require explicit human review.
- The system must support rollback or deactivation of promoted guardrails or learnings.
- Review outputs must be understandable without reading raw logs end to end.
- The framework must support persona-specific overlays without losing shared base-level auditability.

**Technical Enablers:**
- Review summary generation
- Metadata for provenance and approval state
- Rollback/deactivation conventions
- Shared base plus persona overlay structure for promoted artifacts

---

## Design Notes

The key design principle is that self-improvement should be **semi-automatic, not magical**. Clawrari should do the boring capture and review work automatically, but promotion into durable behavior must remain inspectable and, in many cases, reviewable. Blind autonomy is how assistants drift.

The second principle is **graduated durability**. Not every signal deserves to become a permanent rule. The framework should support movement from raw event → reviewed signal → promoted learning → operating guardrail.

The third principle is **operational usefulness over novelty**. A self-improvement system that produces beautiful logs but no behavioral change is useless. Each loop should have a clear path to changing future behavior.

---

## Success Metrics

This feature is successful when:
- Repeat mistakes decline over time for tracked regression categories
- User corrections are incorporated with fewer repeats of the same feedback
- A meaningful share of promoted learnings remain useful after review rather than being reverted
- Prediction confidence becomes better calibrated across repeated reviews
- Friction reviews result in concrete improvements to prompts, skills, or workflows
- The user can point to visible behavioral improvement without needing to manually re-teach the assistant each week

---

## Content Angle

This feature has an obvious story: most assistants are configurable, but very few are systematically self-improving. Clawrari can show the concrete operating loops behind the claim "your AI gets better every day." That is both product value and thought-leadership fuel.

---

## Open Questions

- What is the minimum review cadence that keeps the framework useful without creating admin overhead?
- Which promotions should be automatic versus review-gated in the default install?
- How much schema standardization is enough before the framework becomes too rigid for personal setups?
- Should benchmark cases ship as generic templates only, or also include opinionated starter suites?
- What summary view gives the most trust with the least ceremony?
