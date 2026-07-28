# FRD-C9: Deep Research Pipeline

**Status:** Draft — Gated on v0.2 architecture review (v0.1 failed eval)
**Owner:** Prasith + Claw
**Date:** April 24, 2026
**Promotion gate:** Pending overnight test run (2026-04-24/25). Promote to "Active Feature" only if the v0.1 test produces a publishable report with real contradictions surfaced and citation integrity intact.

---

## Summary

The Deep Research Pipeline is Clawrari's answer to the single-shot AI research failure mode. Most AI research assistants — ChatGPT Deep Research, Claude, Perplexity — do one pass: search, collect, summarize. They smooth contradictions, treat different phrasings as independent evidence, and "deep fry" findings as the same context is re-read across stages. The result is reports that read confident but don't survive scrutiny.

Clawrari's pipeline replaces that pattern with three isolated stages — **gather → analyze → write** — each running as a separate subagent with no shared context. Findings flow forward through clean structured files, not accumulated chat history. The researcher gathers verbatim quotes with adversarial query expansion. The analyst sees only the researcher's structured output, not the raw search results. The writer sees only the analyst's deduped synthesis, not the messy gathering phase. Every claim traces to a source. Contradictions get their own section, not a smoothed-over narrative.

This pattern makes deep research a first-class capability inside Clawrari rather than a wrapper around someone else's API. It runs against any topic where multi-source synthesis matters, ships citation-backed reports to the workspace, and keeps the audit trail (raw findings, structured analysis, run log) for review.

### In Scope
- A reusable skill at `skills/research-deep/` with stage prompts and an orchestrator
- Three-stage pipeline with hard context isolation between stages
- Reflection loop in the gather stage with parallel query expansion (semantic, specific, adversarial, recency)
- Citation-integrity enforcement at every stage (verbatim quotes → deduped facts → numbered inline citations)
- Cron-fireable for scheduled or overnight research without main-session involvement
- Outputs land in `reports/research-<slug>-<date>/` with raw findings, analysis, final report, and run log

### Out of Scope (v0.1)
- Internal data source connectors (Drive, Slack, GitHub etc — would replicate the Onyx model; deferred)
- Confidence weighting in writer output (confidence is in analysis.json but not surfaced inline as `[high]`/`[medium]`/`[low]`)
- Cross-run dedup (preventing re-research of the same topic in a short window)
- Voice layer (out of scope for any near-term version)
- Auto-spawn from main session "research X" intent (stays explicit for now)

---

## User Entry Points

A user invokes the deep research pipeline in three ways:

1. **Cron-scheduled overnight research.** Add a one-shot cron job pointing at the orchestrator prompt. Wake up to a finished report. Use case: "research X tonight, I want to read it tomorrow."
2. **Main session delegation.** From an active session, spawn the orchestrator as a subagent for ~60-90 min. Use case: "kick off a real research dive while we work on something else."
3. **Skill discovery.** When the assistant detects a question that fits the deep-research pattern (multi-source, contested, citation-needed), it proposes the pipeline rather than answering directly. Use case: "what's the actual landscape of vertical AI in healthcare staffing?" — instead of guessing, propose a research run.

The user never directly drives the three subagents. They drive the topic and the timing; the pipeline drives the rest.

---

## Architecture

### Three stages, hard isolation

```
Researcher (gather)        Analyst (synthesize)       Writer (report)
─────────────────         ────────────────────         ─────────────────
Tools: web search,        Tools: NONE                  Tools: NONE
       web fetch,         Input: raw-findings.json     Input: analysis.json
       browser            Output: analysis.json        Output: final-report.md
Cycles: 3-5
Output: raw-findings.json
```

Each stage runs as an isolated subagent (Gemini 3.1 Pro, lightContext: true). Context never crosses stages directly. The next stage reads the previous stage's clean output file from disk.

**Why this matters:** the original Onyx research (referenced below) demonstrated that single-context multi-pass research degrades each downstream interpretation. They call this "deep frying." Hard file boundaries between stages force re-reading clean structured output, not the messy chain-of-thought intermediate state.

### The five patterns we adopted

From the Onyx + CrewAI deep-researcher playbook, applied to our infrastructure:

1. **Stage separation.** Gather, analyze, and write are different jobs. Different agents. No shared context window.
2. **Reflection in the loop.** After every research cycle, the researcher emits a structured reflection block: covered, gaps, new directions, more cycles needed. Static plans don't survive contact with findings.
3. **Parallel query expansion.** Each cycle generates 3-5 query variants — semantic rephrasing, specific narrowing, adversarial angles, recency checks. Skip adversarial queries and hallucinations enter.
4. **LLM selection before synthesis.** The researcher reviews retrieved content and keeps only the relevant chunks before the analyst sees anything. The article is explicit: skip this step and contradictions get smoothed.
5. **Citation integrity.** Verbatim quotes at gather time, deduped + numbered + traceable at write time. Every final claim traces to a source.

### What we deliberately skipped

- **Onyx as infrastructure.** Onyx is an open-source RAG layer for indexing internal data sources. We're not building an enterprise RAG product — we're researching public web topics. The Onyx *patterns* translate; the Onyx *infrastructure* doesn't fit.
- **CrewAI as a framework.** We have `sessions_spawn` and the model playbook. CrewAI would be redundant tooling.
- **Voxtral as voice layer.** Not relevant to Clawrari's current scope.

---

## Implementation

### File layout

```
skills/research-deep/
├── SKILL.md                              # spec, architecture, usage
├── prompts/
│   ├── orchestrator.md                   # top-level coordinator
│   ├── stage-1-researcher.md             # gather + reflect
│   ├── stage-2-analyst.md                # synthesize
│   └── stage-3-writer.md                 # report
├── scripts/
│   └── (legacy, unused — CLI route blocked by plugins.allow)
└── examples/
    └── queue-test-run.md
```

### Cron pattern

```yaml
schedule: at <ISO-timestamp>
sessionTarget: isolated
payload:
  kind: agentTurn
  model: google/gemini-3.1-pro-preview
  thinking: high
  lightContext: true
  timeoutSeconds: 5400  # 90 min covers all 3 stages
  message: |
    Read the orchestrator prompt at
    <path-to-substituted-orchestrator-prompt> and execute it.
delivery:
  mode: none  # report file is the deliverable
```

The orchestrator prompt is pre-substituted (topic, slug, run dir, cycle max) and saved to `tmp/` before queuing. The cron loads it from disk at fire time.

### Output artifacts

Every run produces:

```
reports/research-<slug>-<YYYY-MM-DD>/
├── raw-findings.json       # researcher's audit trail
├── analysis.json           # analyst's structured synthesis
├── final-report.md         # the deliverable
└── run-log.md              # orchestrator's pipeline log
```

The structured intermediates are kept for audit. If the writer's report has a problem (missed contradiction, weak sourcing), the analyst's output is right there to debug against. If the analyst missed something, the researcher's raw findings show whether the gap was in gathering or synthesis.

---

## Promotion Criteria

This FRD ships as **Draft** until the v0.1 evaluation completes. The evaluation is defined in a dedicated eval-design document, following the same pattern as the Active Memory eval (2026-04-17).

**Eval design:** `reports/research-deep-eval-design-2026-04-24.md`

### What the eval measures

The pipeline is scored against a **single-shot baseline** (one Gemini Pro subagent doing the same research in one pass) on a **10-dimension scorecard**:

1. Citation density
2. Source diversity
3. Adversarial coverage
4. Contradiction surfacing
5. Verbatim quote integrity
6. Confidence calibration
7. Gap identification
8. Structural cleanliness
9. Time efficiency
10. Smell test (would Prasith forward this report)

### Scoring rubric

- **8-10 dimensions pass** (pipeline beats or matches baseline): Promote to **Active Feature**. Baseline retired for serious research work.
- **6-7 dimensions pass:** Promote to **Active Feature**, but file follow-ups for specific stage prompts to harden.
- **4-5 dimensions pass:** Hold v0.1 in **Draft**. Iterate on the worst-performing dimensions, re-run.
- **0-3 dimensions pass:** Architecture rethink. Stage isolation may not be earning its complexity.

### Tiebreaker rule

If pipeline and baseline tie or pipeline only modestly outperforms (8 vs 7 dimensions), pipeline still wins ONLY IF dimensions 3, 4, and 5 (adversarial coverage, contradiction surfacing, verbatim integrity) all pass for the pipeline. Those are the structural wins the architecture is supposed to deliver — if they don't show up in the run, the architecture isn't earning its complexity over single-shot.

### Pre-registered predictions

Five predictions are pre-registered in the eval design with confidence levels (60-80%). They get resolved as part of the eval review. If P1 falls (pipeline fails to beat baseline on the structural dimensions it was designed for), that's the strongest signal that v0.1 needs architecture work, not prompt tuning.

---

## v0.1 Evaluation Run

**Topic:** *"How are vertical AI agent products in healthcare staffing differentiating their go-to-market in 2026? What patterns work, what's saturated, what's still open?"*

**Why this topic:**
- Genuinely contested space (multiple opinions, varying data quality)
- Adjacent to Prasith's WorkConnect work — useful regardless of pipeline outcome
- Forces multi-source synthesis (no single article answers it)
- Has natural adversarial queries (failures, "rebranded VMS" critique, saturation arguments)
- Falsifiable claims involved (specific companies, specific metrics)

### Three runs, head-to-head

| Run | Cron job ID | Fires | Output |
|---|---|---|---|
| **A. Pipeline (research-deep v0.1)** | `bfee0826-1d0b-4a85-abb7-92f9c7f26448` | 2026-04-25 03:00 UTC | `reports/research-ai-healthcare-staffing-gtm-2026-2026-04-25/` |
| **B. Baseline (single-shot)** | `9d9f65ba-2282-4111-91a9-17edf215cdd0` | 2026-04-25 04:30 UTC | `reports/research-baseline-single-shot-ai-healthcare-staffing-2026-04-25.md` |
| **C. Eval review (10-dim scorecard, head-to-head)** | `6ec5f855-a639-452b-afff-90c9e507679a` | 2026-04-25 13:30 UTC (09:30 EDT) | `reports/research-deep-eval-results-2026-04-25.md` |

The eval review subagent reads both A and B, applies the 10-dimension scorecard from `reports/research-deep-eval-design-2026-04-24.md`, resolves the 5 pre-registered predictions, and updates THIS FRD from Draft → Active (or holds it) based on the rubric.

### Why a baseline matters here

Without a baseline, we can't tell if the pipeline's complexity is earning its keep. The pipeline costs ~3x the wall-clock and ~3x the model calls of single-shot. If single-shot produces a comparably good report, the pipeline is overkill. If pipeline produces a measurably better report on dimensions 3, 4, 5 (the structural wins), the architecture is justified.

## v0.1 Evaluation Result & v0.2 Architecture Review Needed

**Eval Date:** 2026-04-25
**Result:** Failed (Hold / Rearchitect)
**Eval Report:** `reports/research-deep-eval-results-2026-04-25.md`

### Summary of v0.1 Eval
The v0.1 pipeline was tested head-to-head against a single-shot Gemini Pro baseline. While the pipeline scored mechanically higher (8/10 vs 5/10), it **failed the structural dimensions and the smell test**, invoking the hard rule to hold the feature.

**Key failures:**
1. **Verbatim Quote Integrity (Dimension 5):** The pipeline hallucinated/paraphrased quotes despite explicit prompts to keep them verbatim. The Writer stage modified text inside quotes to fit the narrative.
2. **Smell Test (Dimension 10):** The isolated Researcher stopped after just 2 cycles, thinking it reached "saturation." The resulting report was extremely thin, openly admitted to having zero high-confidence findings, and lacked the depth needed for a board-ready report. The single-shot baseline actually outperformed the pipeline in source diversity (11 vs 9 domains) and narrative confidence.

### v0.2 Architecture Review Needed
Because the architecture failed on its core mandate—preventing "deep fried" hallucinations and ensuring structural depth—prompt tuning alone is insufficient. The v0.2 architecture must address:

1. **Cycle Minimums & Depth Enforcement:** The Researcher stage needs strict `cycles_min` bounds and a better heuristic for "saturation" (e.g., must find at least 3 independent sources for 3+ themes before stopping).
2. **Reviewer Loop:** The Analyst stage must act as a gatekeeper. If the Researcher's `raw-findings.json` is too thin, the Analyst should reject the payload and kick it back to the Researcher for another cycle.
3. **Hard Verbatim Safeguards:** The Writer stage needs absolute system-level prohibitions against altering quotes. Paraphrasing inside quotation marks must be solved, potentially by having the Writer insert citations as structural keys that are substituted verbatim post-generation.

---

## Open Questions

1. **Cycle cap calibration.** v0.1 caps at 3 cycles. Onyx caps at 8. Is 3 enough for genuine deep research, or are we shipping a half-pipeline? First eval will tell us.
2. **Confidence inlining.** Analysis.json has confidence per claim, but the writer prompt doesn't surface confidence inline beyond "watchlist" and `[medium-confidence]` markers. Should every claim get a visible confidence flag, or does that clutter the report?
3. **Orchestrator failure modes.** If a stage fails mid-run (subagent timeout, file write failure), the orchestrator currently aborts and leaves partial outputs. Should there be a resume-from-stage-N capability, or is "rerun from scratch" simpler?
4. **Internal sources.** When does it become worth adding a Drive/Slack connector? Probably when the operator asks a question whose answer is partly in their own files. Defer until that happens.
5. **Skill auto-discovery.** Should the assistant proactively suggest the deep research pipeline when a question fits the pattern, or stay explicit-invocation-only? Current default: explicit. Reconsider after 5+ runs.

---

## References

- **Source playbook:** Akshay Pachaar, "How to build a Deep Researcher" (X thread, 2026-04-23). The Onyx + CrewAI architecture that this skill adopts in pattern but not infrastructure.
- **Onyx DeepResearch Bench result:** #1 ahead of OpenAI Deep Research, Gemini 2.5 Pro, Perplexity Deep Research. Validates that the multi-stage isolated pattern actually wins on PhD-level research benches.
- **Skill location:** `~/.openclaw/workspace/skills/research-deep/`
- **Related FRDs:** C3 (Self-Improvement Framework — this FRD becomes a feedback loop input when we evaluate the v0.1 run quality), C8 (Assistant Slack identity — research reports may eventually post summaries to the assistant's briefing channel).
