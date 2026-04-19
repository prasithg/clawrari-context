# clawrari-context

> **The product development context for [Clawrari](https://github.com/prasithg/clawrari) — open-sourced.**
>
> FRDs, architecture blueprints, mission, values, process docs, and work orders. All of it, public.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Built with OpenClaw](https://img.shields.io/badge/Built%20with-OpenClaw-blue)](https://github.com/openclaw/openclaw)

---

## Why This Exists

Most AI-first builders hide their process. This is the opposite.

Every FRD, every blueprint, every decision is here — publicly. Not because it's perfect, but because watching how decisions actually get made is more useful than reading finished artifacts.

If you're trying to figure out how to actually run agentic engineering — not just talk about it — this is a case study in progress. Fork it for your own AI assistant context, or just read it as a study guide.

> "Most AI-first builders hide their process. This is the opposite — every FRD, blueprint, and decision, publicly. Useful for people trying to figure out how to actually run agentic engineering."

---

## What Is Clawrari?

The open-source, high-performance reference operating system for [OpenClaw](https://github.com/openclaw/openclaw). Two personas (Work + Personal) sharing a performance-optimized core. Built by [@prasithg](https://github.com/prasithg), open-sourced so everyone can see the methodology — not just the code.

**Tagline:** The reference OS for the AI-native builder.

**Main repo:** [github.com/prasithg/clawrari](https://github.com/prasithg/clawrari)

---

## How To Use This Repo

**Option 1: Fork it as your own context repo**

Fork this repo and use it as the product development context for your own OpenClaw or AI assistant setup. Replace the mission/vision/values with yours, then follow the same FRD → Blueprint → Work Order cycle for every new capability you build.

**Option 2: Read it as a case study**

Read through the FRDs and blueprints to understand how we think about AI assistant features — memory architecture, self-improvement loops, persona design. The `docs/process/` folder explains the methodology.

---

## Repository Structure

```
clawrari-context/
├── README.md                    ← You are here
├── docs/
│   ├── company-core/            ← Mission, vision, values, who we serve
│   │   ├── mission.md
│   │   ├── vision.md
│   │   ├── values.md
│   │   └── who-we-serve.md
│   └── process/                 ← How we work (8090 adapted for open source)
│       ├── product-development-process.md
│       └── feature-pipeline.md
├── features/                    ← Discovery briefs + FRDs + blueprints
├── notes/                       ← Session notes, design decisions
└── work-orders/                 ← Atomic units for coding agents
```

---

## The Development Cycle

Every feature moves through: **Discover → Define → Architect → Build → Ship**

| Phase | Output | Who |
|-------|--------|-----|
| Discover | Validated Brief | Prasith + Claw |
| Define | FRD (requirements) | Prasith + Claw |
| Architect | Blueprint + Work Orders | Claw + coding agents |
| Build | Merged PRs | Coding agents (Claude Code, Codex) |
| Ship | Released version | Prasith reviews + publishes |

Adapted from the [8090 Product Development Process](docs/process/product-development-process.md).

---

## Current Feature Status

| Feature | Status | Doc |
|---------|--------|-----|
| Core Architecture (dual-persona + shared base) | Discovery | `features/discovery-core-architecture.md` |
| Memory Architecture | FRD + Blueprint | `features/frd-c2-memory-architecture.md` |
| Self-Improvement Framework | FRD | `features/frd-c3-self-improvement.md` |
| Personal Persona | Research | `features/personal-persona-patterns.md` |
| PrasClaw Slack Identity | Brief | `features/frd-c8-prasclaw-slack-identity.md` |

---

## Why This Repo Is Public

This isn't just specs — it's a demonstration of how to run product development with AI agents. Every decision, every FRD, every work order is visible. Learn from it, fork it, improve it.

Building in public isn't a marketing strategy. It's the operating model.

---

## License

MIT — see [LICENSE](LICENSE).

---

*Made by [@prasithg](https://github.com/prasithg) · Part of the [Clawrari](https://github.com/prasithg/clawrari) project*
