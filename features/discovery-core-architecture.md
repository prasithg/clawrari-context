# Discovery Brief: Core Architecture (Dual-Persona + Shared Base)

**Status:** Validated
**Owner:** Prasith + Claw
**Date:** April 8, 2026

---

## Thesis

Clawrari's core value is a dual-persona architecture (Work + Personal) sharing a high-performance base layer. The base contains memory patterns, self-improvement loops, and productivity fundamentals. Each persona adds domain-specific skills, integrations, and crons. This architecture lets one person run two focused AI assistants from a single, maintainable codebase.

## Target User and Current Workflow

**Primary:** Technical founder/CTO using OpenClaw daily.

**Today:** They have a single OpenClaw instance with a growing mess of skills, crons, and memory files. Work and personal contexts bleed together. Adding a second instance means starting from scratch or manually duplicating config. There's no clean separation between "enterprise assistant" and "personal assistant" behaviors.

**The pain:** Either they run one overloaded instance that tries to do everything, or they maintain two completely separate setups with no shared foundation.

## Proposed Capability

A clean architectural split:

```
clawrari/
├── base/           # Shared: memory, self-improvement, core skills, templates
├── personas/
│   ├── work/       # Enterprise: 8090, GWS, Slack, coding agents
│   └── personal/   # Personal: social, content, Discord, lifestyle
└── bootstrap/      # Installer that sets up one or both personas
```

Each persona is an independent OpenClaw instance with its own:
- SOUL.md (personality, tone, directives)
- USER.md (context about who they're helping)
- memory/ (separate memory stores)
- Channel connections (Work = Telegram/Slack, Personal = Discord/SMS)

Both personas share:
- Core skills (mac-screenshots, web search, browser, etc.)
- Self-improvement loops (regressions, predictions, RLHF)
- Memory architecture templates
- Reference docs (prompt patterns, conventions)
- Heartbeat / health check patterns

## Evidence Summary

**Personal experience:**
- We've been running this exact pattern informally — our workspace has work-oriented and personal-oriented capabilities mixed together
- The desire for a "personal Claw" separate from "work Claw" came directly from Prasith
- Dual-instance is technically straightforward — two OpenClaw installations pointing at different workspace directories

**Community signal:**
- OpenClaw users regularly ask about multi-instance setups in Discord
- Cursor.directory proves demand for shareable, opinionated AI configurations
- Oh My Zsh's success shows opinionated framework layers work on top of powerful tools
- ClawHub has 10K+ skills but no "full stack operating system" packages

**Competitive intelligence:**
- No existing "dual-persona" or "multi-instance" OpenClaw framework
- GPT Store / Custom GPTs normalized the idea of purpose-built AI personas
- Claude Projects are single-purpose by design — similar pattern

## Counter-Evidence and Qualifications

- The dual-persona split may be too autobiographical — most users might only want one persona
- Maintaining shared base + two personas adds complexity vs. a single opinionated setup
- The "personal" persona is less well-defined than the "work" persona
- Platform risk: if OpenClaw ships native multi-instance or persona support, the architecture layer becomes less valuable

**Mitigation:** Ship core + Work persona first. Personal persona is Phase 2 — validated by actual usage, not assumption.

## Scope Boundary

**In for first build:**
- Shared base layer (memory templates, self-improvement, core skills, reference docs)
- Work persona (8090, GWS, Slack, coding agents, responsive checks, morning briefing)
- Bootstrap installer for single-persona setup
- Documentation explaining the architecture

**Out for first build:**
- Personal persona (Phase 2)
- Dual-instance sync mechanism (Phase 2 — needs real testing)
- clawrari.com website
- ClawHub publishing
- Community contribution workflows

## Risk Assessment

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Too personalized to Prasith's setup | Medium | High | Abstract aggressively during packaging |
| OpenClaw ships competing feature | Low-Medium | High | Move fast, establish community position |
| Maintenance burden of open source | Medium | Medium | Automated testing, CI, clear contribution guidelines |
| Low adoption despite good content | Medium | Low | Content value persists regardless of install count |

## Kill Criteria

- After 90 days of sustained posting + community engagement: < 50 GitHub stars AND < 5 contributors → evaluate continued investment
- If users consistently ignore the dual-persona split → drop it, ship as single-persona OS
- If maintaining the open source repo takes > 5 hours/week without proportional content/brand value → simplify dramatically

## Recommendation

**Build.** Start with the shared base + Work persona. This is low risk because we're essentially packaging what we already run. The personal persona is Phase 2 content once the base is proven.

## North Star Metric

**Weekly active retained installs that complete at least one repeatable workflow using Clawrari defaults.**

Not stars. Not clones. Active usage of the operating system layer.
