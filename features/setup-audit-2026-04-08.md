# Clawrari Setup Audit — April 8, 2026

Comprehensive catalog of everything in our current workspace that could be generalized into Clawrari. Organized by category with Clawrari-readiness assessment.

## Legend
- ✅ **Ready** — Stable, generalizable, used daily. Package as-is.
- 🔄 **Needs Work** — Good pattern but too Prasith-specific or incomplete. Needs abstraction.
- ⏭️ **Skip** — Too specific, not generalizable, or better left as personal config.
- 🆕 **New for Clawrari** — Doesn't exist in our setup yet but should be built for Clawrari.

---

## 1. Workspace Templates (Base Layer)

These are the files every Clawrari install gets. Our current versions are the reference.

| File | Status | Notes |
|------|--------|-------|
| `SOUL.md` | 🔄 | Needs templatization. Current one is Prasith-specific (swearing, car references). Need a generator that asks personality questions. |
| `USER.md` | 🔄 | Needs templatization. Current one has Prasith's specifics. Need a generator. |
| `AGENTS.md` | ✅ | Already fairly generic. The "read SOUL → USER → session-brief" startup sequence is universally useful. |
| `HEARTBEAT.md` | 🔄 | Structure is great (health checks, periodic checks, tool version). Content references our specific tools (slk, qmd, gws). Need to make modular. |
| `IDENTITY.md` | ✅ | Simple, clean. Name/creature/vibe/emoji. Easy to template. |
| `MEMORY.md` | ✅ | The index-of-memory-files pattern is universally useful. |
| `TOOLS.md` | 🔄 | Structure is good (local notes for env-specific things). Content is all our specifics. Template with examples. |
| `SECURITY.md` | ✅ | Good as reference. Security posture documentation is universally needed. |

**Clawrari action:** Bootstrap generator asks questions → fills these templates.

---

## 2. Memory Architecture (Base Layer)

Our memory system is one of Clawrari's strongest differentiators.

| Component | Status | Notes |
|-----------|--------|-------|
| `memory/session-brief.md` | ✅ | 50-line preconscious buffer. Load first every session. Universally applicable. |
| `memory/subagent-ledger.md` | ✅ | Append-only tracking of spawned agents. Check for stale rows on startup. |
| `memory/YYYY-MM-DD.md` daily logs | ✅ | Raw daily logging. Universal pattern. |
| `memory/projects.md` | 🔄 | Structure is great. Content is Prasith's projects. Template with examples. |
| `memory/people.md` | 🔄 | Structure is great. Content is Prasith's contacts. Template. |
| `memory/preferences.md` | 🔄 | Same — good structure, needs templatization. |
| `memory/regressions.md` | ✅ | Failure-to-guardrail pipeline. This is a killer feature. Ship as-is with example regressions. |
| `memory/rules-constitutional.md` | 🔄 | Our 10 hard rules are Prasith-specific. Need a framework for users to write their own. |
| `memory/rules-tactical.md` | 🔄 | Evolving conventions. Structure is great, content is ours. |
| `memory/context-holds.md` | ✅ | Active context filters with expiry. Novel pattern, universally useful. |
| `memory/predictions.md` | ✅ | Prediction-outcome calibration. Advanced but powerful. |
| `memory/influences.md` | ⏭️ | Too personal. Skip for Clawrari. |
| `memory/reference/conventions.md` | ✅ | Type-tag + trust score convention. Good reference material. |
| `memory/heartbeat-state.json` | ✅ | State tracking for periodic checks. Universal. |

**Clawrari action:** Ship the architecture with empty templates + our filled versions as examples.

---

## 3. Self-Improvement Systems (Base Layer)

This is the "your AI gets better every day" promise.

| System | Status | Notes |
|--------|--------|-------|
| `systems/failures/failure-log.md` | ✅ | Structured failure tracking. Universal. |
| `systems/self-improving/` | ✅ | Learning log, query, promote scripts. Novel. |
| `systems/prediction-log/` | ✅ | Prediction tracking + metrics. Advanced but valuable. |
| `systems/annotation-loop/` | ✅ | Pattern learning from annotations. |
| `systems/auto-optimize/` | 🔄 | Benchmarking and optimization loop. Needs cleanup. |
| `systems/model-eval/` | ✅ | Subagent comparison framework. |
| `systems/agent-registry/running.json` | ✅ | Active agent tracking. Simple and effective. |
| Regressions pipeline (in memory/) | ✅ | Named regressions loaded every session. The crown jewel. |
| RLHF loop (in HEARTBEAT.md) | ✅ | Check #prasclaw-briefings → apply corrections. Generalizable to any feedback channel. |
| Recursive Self-Improvement Cycle (in SOUL.md) | ✅ | Generate → Evaluate → Diagnose → Improve → Repeat. Framework, not code. |

**Clawrari action:** Package as "Self-Improvement Framework" — the core differentiator.

---

## 4. Skills — Work Persona

### Custom-Built (Ours)
| Skill | Status | Notes |
|-------|--------|-------|
| `morning-briefing` | ✅ | Calendar + Slack + tasks + weather. Universally useful. Needs connector abstraction. |
| `responsive` | ✅ | Scan for unanswered messages, prioritize by urgency/age. Great for any team lead. |
| `daily-digest` | ✅ | Research + trend detection + content drafts. Strong. |
| `content-engine` | ✅ | Multi-platform content pipeline. Work persona staple. |
| `ai-talk-draft` | 🔄 | Specific to our content format. Generalize into content-engine. |
| `content-autopilot` | ✅ | Automated content generation pipeline. |
| `meeting-debrief` | ✅ | Post-meeting synthesis. Universal. |
| `night-work` | ✅ | Overnight task execution framework. Unique to Clawrari. |
| `auto-board-sync` | 🔄 | Kanban sync. Specific to our PinchChat setup. |
| `research` | ✅ | Deep research workflow. Universal. |
| `summarize` | ✅ | Content summarization. Universal. |
| `mac-screenshots` | ✅ | Just built today. Clean, well-documented. |
| `video-to-spec` | ✅ | Competitor analysis from video. Niche but powerful. |
| `publish-pipeline` | 🔄 | Our specific publishing flow. Needs abstraction. |
| `data-ingestion` | 🔄 | Data pipeline management. Needs review. |
| `linear` | ✅ | Linear project management integration. |
| `pinchbench` | ⏭️ | Our internal benchmarking. Skip. |

### GWS (Google Workspace) Skills
| Skill | Status | Notes |
|-------|--------|-------|
| `gws-*` (50+ skills) | ✅ | Massive Google Workspace integration. These are community skills from ClawHub, not ours to package. But reference config for them goes in Work persona. |

### Persona Skills
| Skill | Status | Notes |
|-------|--------|-------|
| `persona-exec-assistant` | ✅ | Good reference for Work persona defaults. |
| `persona-project-manager` | ✅ | Same. |
| `persona-researcher` | ✅ | Same. |
| `persona-content-creator` | ✅ | Bridges Work → Personal. |
| Others (6 more personas) | 🔄 | Evaluate which to include vs. leave as examples. |

### Integration Skills
| Skill | Status | Notes |
|-------|--------|-------|
| `slack` | ✅ | Slack integration. Work persona essential. |
| `slack-personal` | ✅ | Personal Slack integration. |
| `figma` | ✅ | Design tool integration. |
| `google-doc-publish` | ✅ | Doc publishing workflow. |

---

## 5. Skills — Personal Persona

### Existing
| Skill | Status | Notes |
|-------|--------|-------|
| `content-engine` | ✅ | Shared with Work but personal brand focus. |
| `daily-digest` | ✅ | Personal version: Reddit, X, community trends. |
| `mac-screenshots` | ✅ | Life/productivity. |

### From YouTube Video (7 patterns — Prasith requested)
| Pattern | Status | Notes |
|---------|--------|-------|
| Voice Journal | 🆕 | Voice → transcription → daily markdown → highlights. Needs: whisper API skill + journal template. |
| Wallet Tracker | 🆕 | Expense/income logging → balance → summaries. Needs: structured data skill. |
| Morning Brief (personal) | 🔄 | Already have morning-briefing but it's work-focused. Personal version: weather + personal calendar + personal todos + news topics. |
| Subscription Tracker | 🆕 | Email invoice scanning for recurring charges. Needs: email parsing + tracker template. |
| Listen Mode | 🆕 | Text/articles → TTS audio files. Needs: TTS skill + article extraction. |
| Health Tracker | 🆕 | Meals/symptoms → markdown → weekly patterns. Needs: structured logging skill. |
| Habit Tracker | 🆕 | Check-ins + weekly completion + streaks. Needs: habit tracking skill. |

### Social Engagement (Alex Finn patterns)
| Pattern | Status | Notes |
|---------|--------|-------|
| Algorithmic content optimization | 🆕 | Analyze post performance → optimize hooks/timing/CTAs. |
| Reply engagement strategy | 🔄 | `x-reply-guy` skill designed but not built. |
| Audience analysis | 🆕 | Track follower growth, engagement patterns, best-performing content types. |
| Cross-platform repurposing | 🆕 | Twitter → LinkedIn → blog. Automated reformatting. |

---

## 6. Cron Patterns (Both Personas)

| Cron | Persona | Status | Notes |
|------|---------|--------|-------|
| Morning Briefing | Work | ✅ | 6:45 AM. Calendar + Slack + tasks. |
| Responsive Check | Work | ✅ | 10 AM + 3 PM. Unanswered message scan. |
| Daily Digest | Both | ✅ | 10 PM. Research + trends + content drafts. |
| Heartbeat | Both | ✅ | Every 2 hours. Health checks + Slack scan. |
| QMD Reindex | Base | ✅ | Triggered when stale >24h. Memory search freshness. |
| Community Scan | Personal | ✅ | Weekly Sunday. Reddit, X, ClawHub trends. |
| Model Freshness Check | Base | ✅ | Weekly Sunday. Scan for new models. |
| Tool Version Check | Base | ✅ | Weekly. Check for updates. |
| Night Work | Both | ✅ | On sleep. Execute task queue. |

---

## 7. Reference Documents

| Document | Status | Notes |
|----------|--------|-------|
| `reference/execplan-template.md` | ✅ | ExecPlan for coding agents. Core Clawrari asset. |
| `reference/agent-prompt-template.md` | ✅ | XML-structured prompt patterns. |
| `reference/prompt-engineering-patterns.md` | ✅ | Full XML block library. |
| `reference/architecture-template.md` | ✅ | ARCHITECTURE.md template for projects. |
| `reference/coding-standards.md` | ✅ | Agent reinforcement coding standards. |
| `reference/sop-agent-task-workflow.md` | ✅ | Agent task SOP. |
| `reference/validation-agent-spec.md` | ✅ | Cross-model validation spec. |
| `reference/episode-template.md` | ✅ | Agent session episode notes template. |
| `reference/willison-agentic-engineering-patterns.md` | ✅ | Community patterns reference. |
| `reference/agentic-engineering-patterns.md` | ✅ | Our agentic engineering patterns. |

---

## 8. Scripts & Tooling

| Script | Status | Notes |
|--------|--------|-------|
| `scripts/run-claude-code.sh` | ✅ | Claude Code wrapper (handles API key conflict). Essential. |
| `scripts/run-codex.sh` | ✅ | Codex wrapper (handles git init + stdin piping). Essential. |
| `scripts/qmd-reindex.sh` | ✅ | QMD memory reindex. Universal. |
| `scripts/responsive_update.js` | 🔄 | Our specific Slack channels. Needs abstraction. |
| `scripts/github-context.js` | 🔄 | Our specific repos. Needs abstraction. |
| `scripts/publish-pipeline.py` | 🔄 | Our specific publishing flow. |
| `scripts/build-custom-pptx.py` | ⏭️ | Project-specific. Skip. |
| `scripts/update_tracker.py` | ⏭️ | Project-specific. Skip. |

---

## Summary: Clawrari Feature Map

Based on this audit, here's how features group:

### FRD-C1: Core Architecture (Dual-Persona + Shared Base)
*Already written as discovery brief.*
- Workspace template system (SOUL, USER, AGENTS, HEARTBEAT, IDENTITY, MEMORY, TOOLS)
- Bootstrap generator
- Persona separation architecture
- Base layer definition

### FRD-C2: Memory Architecture
- Session brief pattern
- Daily logs
- Subagent ledger
- Regressions pipeline
- Context holds
- Predictions log
- People / projects / preferences templates
- QMD integration for semantic search

### FRD-C3: Self-Improvement Framework
- Regressions pipeline (failure-to-guardrail)
- RLHF loop (feedback → permanent behavioral change)
- Prediction-outcome calibration
- Autoresearch / recursive self-improvement cycle
- Friction detection
- Auto-optimize system
- Learning log / promote / query

### FRD-C4: Work Persona — Enterprise Operations
- Morning briefing cron
- Responsive check system
- Daily digest cron
- Coding agent orchestration (Claude Code + Codex wrappers)
- 8090 process templates (ExecPlan, FRD standard, Blueprint, Work Orders)
- Slack integration patterns
- Google Workspace integration patterns
- Meeting debrief
- Night work framework

### FRD-C5: Personal Persona — Life & Brand
- Content engine + publishing pipeline
- Social engagement patterns (Alex Finn)
- 7 personal automation patterns (from YouTube video)
- Community scan cron
- Discord integration patterns
- Personal morning brief (weather + personal calendar + todos)

### FRD-C6: Skill Library
- Package individual skills to ClawHub
- Skill development standards (using skill-creator)
- Quality gates for Clawrari-certified skills
- mac-screenshots as reference implementation

### FRD-C7: Documentation & Site
- clawrari.com
- GitHub README
- Installation guide
- Architecture docs
- Philosophy docs
- Content: "what my AI learned this week" format

---

## Recommended Priority

1. **FRD-C1** (Core Architecture) — already in discovery ✅
2. **FRD-C2** (Memory Architecture) — our strongest differentiator
3. **FRD-C3** (Self-Improvement) — the "gets better every day" promise
4. **FRD-C4** (Work Persona) — most of this already works, needs packaging
5. **FRD-C5** (Personal Persona) — needs most new work
6. **FRD-C6** (Skill Library) — depends on C4/C5 having skills to package
7. **FRD-C7** (Docs & Site) — last, after there's something to document

---

*This audit is the foundation for FRD writing. Each FRD will reference specific items from this inventory.*
