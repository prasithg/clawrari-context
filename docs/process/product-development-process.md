# Product Development Process — Personal Projects

Adapted from the 8090 framework (used in production at a fast-moving startup) for solo founder + AI agent teams.

## The Cycle

Every feature moves through five phases:

```
Discover → Define → Architect → Build → Ship
```

| Phase | What Happens | Output |
|-------|-------------|--------|
| **Discover** | Validate the problem. Is this worth building? | Discovery Brief (in `features/`) |
| **Define** | Write requirements. What exactly are we building? | FRD (in `features/`) |
| **Architect** | Technical decomposition. How do we build it? | Blueprint + Work Orders (in `blueprints/` + `work-orders/`) |
| **Build** | Coding agents execute work orders | Merged PRs, implemented features |
| **Ship** | Review, test, release | Published version |

## Adaptations from 8090

| Full 8090 (Team setup) | Adapted (Personal Projects) | Why |
|---------------------|---------------------------|-----|
| Product + Design + CTO roles | All Prasith + Claw | Solo founder |
| FDD (Functional Design Doc) | Skip — design-in-code | No dedicated designer |
| Linear for status | Status in repo + session-brief | Don't need another tool |
| Multi-sprint scrum | Time-boxed build sprints | Side project cadence |
| Formal discovery interviews | Personal experience + community signal + competitive research | Faster validation |
| Multi-FRD projects | 1-2 FRDs at a time | Smaller scope |

## What We Keep (Non-Negotiable)

1. **Discovery before building.** No FRD without a validated brief. No code without a plan.
2. **Requirements with stable IDs.** Every requirement gets an ID (REQ-XX-001) and acceptance criteria.
3. **Work orders for coding agents.** Atomic units with ExecPlan format and XML sections.
4. **Decision log.** Every "why" gets captured in `docs/decisions/`.
5. **Content hooks.** Every FRD has a "content angle" — what's the story here?
6. **Kill criteria.** Every feature has explicit conditions for stopping.

## What We Add

1. **Vivid Vision** at project level (in `docs/company-core/vision.md`)
2. **Validation scorecard** in discovery briefs
3. **North star metric** per feature
4. **Public methodology** — the repo itself teaches the process
