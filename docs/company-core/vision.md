# Vision

## The 12-Month Picture (Present Tense — Vivid Vision)

Clawrari is the default recommendation when serious people ask how to set up OpenClaw. The repo has strong opinions and a clear architecture: a shared performance core, a Work persona for enterprise operations, and a Personal persona for life optimization and brand building.

It doesn't feel like a template kit. It feels like a living operating system — with defaults, philosophy, upgrade paths, and a community that improves it weekly.

People install it in under 5 minutes. They understand the architecture without reading every file. They immediately have a better setup than 95% of OpenClaw users. The best users aren't just running Clawrari — they're contributing back patterns, skills, and improvements that make everyone's setup better.

The content flywheel runs itself. Every feature shipped, every bug fixed, every pattern discovered becomes a post, a thread, a teardown. Prasith is recognized as one of the definitive voices on AI agent operations — not because he talks about it, but because he builds in public and shows the receipts.

## The Architecture

```
clawrari/
├── base/                    # Shared performance core
│   ├── memory/              # Memory architecture (session-brief, daily logs, semantic search)
│   ├── skills/              # Core skills both personas use
│   ├── templates/           # SOUL.md, USER.md, AGENTS.md, HEARTBEAT.md
│   ├── reference/           # Prompt patterns, ExecPlan template, conventions
│   ├── crons/               # Heartbeat, QMD freshness, health checks
│   └── self-improvement/    # Regressions pipeline, predictions, RLHF, autoresearch
│
├── personas/
│   ├── work/                # Enterprise persona
│   │   ├── skills/          # 8090 process, GWS, Slack, coding agents, responsive checks
│   │   ├── crons/           # Morning briefing, responsive scans, daily digest
│   │   └── integrations/    # Google Workspace, Linear, GitHub, Slack
│   │
│   └── personal/            # Personal persona
│       ├── skills/          # Social engagement, content engine, personal brand, lifestyle
│       ├── crons/           # Community scan, content calendar, social posting
│       └── integrations/    # Discord, social APIs, personal tools
│
├── bootstrap/               # Zero-to-operational installer
├── docs/                    # Philosophy, guides, architecture
└── site/                    # clawrari.com
```

## Two Personas, One Brain

**Work Persona — The Enterprise Multiplier**
8090-style product development. Google Workspace orchestration. Slack-native communication. RLHF-driven improvement. Coding agent orchestration. Structured task management. This is "employee extender" — makes a 10-person team feel like 20.

**Personal Persona — The Life Optimizer**
Social engagement (algorithmic growth, reply strategies, audience building). Personal brand (content engine, posting cadence, voice consistency). Discord community management. Personal workspace maintenance. Lifestyle automation.

**Shared Base — The Performance Core**
Memory architecture. Self-improvement loops (autoresearch, regressions pipeline, prediction calibration, friction detection). Skill building framework. Bleeding-edge techniques. Productivity patterns. This is what makes Clawrari Clawrari — both personas inherit it.

## What Clawrari Is NOT
- A generic template or starter kit (we have opinions and they're strong)
- An OpenClaw fork or wrapper (runs on top of OpenClaw)
- A product for non-technical users (you should be comfortable with CLIs and config)
- A hosted service (local-first, always)
- A SaaS business (it's an open source project + content engine)
