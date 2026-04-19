# Personal Persona — Feature Patterns Inventory

Collected from research, YouTube video shared by Prasith (April 8), Alex Finn patterns, and our own usage.

## From YouTube: 7 Personal Automation Patterns

Source: "How I Use OpenClaw for My Personal Life" — shared by Prasith 4/8

All patterns use Telegram topics (one topic per use case) as the channel interface.

### 1. Voice Journal
**What:** Voice messages → Whisper transcription → daily markdown → weekly highlights
**How it works:** Send a voice note anytime. OpenClaw transcribes via Whisper API, appends to `journal/YYYY-MM-DD.md`. Weekly cron extracts themes, moods, key decisions.
**Clawrari fit:** ✅ High. Universal personal productivity pattern.
**Requires:** `openai-whisper-api` skill (already exists), journal template, weekly synthesis cron.
**Persona:** Personal

### 2. Wallet Tracker
**What:** "Spent $45 on dinner" → structured expense log → balance → monthly summary
**How it works:** Natural language expense input. OpenClaw parses amount, category, date. Appends to `finance/YYYY-MM.md`. Monthly cron generates spending breakdown by category.
**Clawrari fit:** ✅ Medium-High. Common need, but many dedicated apps exist.
**Requires:** Expense parsing skill, structured markdown template, monthly summary cron.
**Persona:** Personal

### 3. Morning Brief (Personal)
**What:** Weather + personal calendar + personal todos + news topics of interest
**How it works:** Cron fires at wake time. Pulls weather API, personal calendar, todo list, news for configured topics. Posts summary to personal channel.
**Clawrari fit:** ✅ High. We already have `morning-briefing` for work. Personal version is weather + personal calendar + lifestyle topics instead of Slack unreads + work tasks.
**Requires:** Weather skill (exists), calendar integration, personal todo tracking, news/topic skill.
**Persona:** Personal (parallel to Work morning briefing)

### 4. Subscription Tracker
**What:** Scans email for recurring charges → builds subscription inventory → alerts on renewals
**How it works:** Periodic email scan for invoice/receipt patterns. Extracts service name, amount, billing cycle. Maintains `finance/subscriptions.md`. Alerts before renewals.
**Clawrari fit:** 🔄 Medium. Useful but requires email access (security concern per our rules).
**Requires:** Email scanning skill (we have email disabled for security), receipt parsing.
**Persona:** Personal
**Note:** Conflicts with our "email access fully disabled" rule. Could work with opt-in Gmail read-only for receipts only. Needs security policy decision.

### 5. Listen Mode
**What:** Send a URL or text → get back an audio file (TTS)
**How it works:** Send article URL or paste text. OpenClaw extracts readable content, generates TTS audio, sends back as voice message.
**Clawrari fit:** ✅ High. We already have `tts` tool. Just needs a skill wrapper.
**Requires:** `web_fetch` + `tts` tool. Article extraction + TTS synthesis skill.
**Persona:** Personal

### 6. Health Tracker
**What:** "Had pasta for lunch, feeling tired after" → structured health log → weekly pattern analysis
**How it works:** Natural language health/meal/symptom input. OpenClaw structures into categories (meals, symptoms, sleep, exercise). Weekly cron analyzes patterns and correlations.
**Clawrari fit:** ✅ Medium-High. Niche but dedicated audience. Privacy-sensitive — local-only data.
**Requires:** Health logging skill, structured template, weekly analysis cron.
**Persona:** Personal

### 7. Habit Tracker
**What:** "Did I meditate? Yes. Run? No." → habit grid → weekly completion report with streaks
**How it works:** Daily check-in prompt (cron or on-demand). Binary yes/no per habit. Tracks streaks, completion rates. Weekly report with trends.
**Clawrari fit:** ✅ High. Simple, universally useful, low complexity.
**Requires:** Habit tracking skill, streak logic, weekly report cron.
**Persona:** Personal

---

## From Alex Finn Research: Social Engagement Patterns

### 8. Algorithmic Content Optimization
**What:** Analyze post performance → optimize hooks, timing, CTAs → improve engagement
**How it works:** After posting, track engagement metrics. AI analyzes what worked (hook style, post time, format, topic). Builds a personal "what works for me" profile over time.
**Clawrari fit:** ✅ High for personal brand building. Core to personal persona promise.
**Requires:** Social API access (X, LinkedIn) or manual metric input, analysis skill.
**Persona:** Personal

### 9. Reply Engagement Strategy
**What:** Find high-value conversations → draft contextual replies → build network
**How it works:** Monitor relevant topics/people. Identify high-engagement threads. Draft replies in user's voice. Present for approval before posting.
**Clawrari fit:** ✅ High. We already designed `x-reply-guy` skill.
**Requires:** `x-reply-guy` skill (designed, not built), social API or browser automation.
**Persona:** Personal

### 10. Audience Growth Dashboard
**What:** Track follower growth, engagement patterns, best-performing content types
**How it works:** Weekly/daily pull of social metrics. Dashboard in markdown or web UI. Trend analysis and recommendations.
**Clawrari fit:** 🔄 Medium. Useful but lots of existing tools do this.
**Requires:** Social API access, metrics storage, trend analysis.
**Persona:** Personal

### 11. Cross-Platform Repurposing
**What:** Twitter thread → LinkedIn post → blog draft. Automated reformatting.
**How it works:** Write once in one format. AI repurposes for each platform (adjusting length, tone, format, hashtags).
**Clawrari fit:** ✅ High. Our `content-engine` already does some of this.
**Requires:** Content engine skill enhancement, platform-specific formatting templates.
**Persona:** Personal (bridges to Work content)

---

## Priority Assessment for FRD-C5

**Tier 1 (Ship first — high value, low complexity):**
1. Habit Tracker — simplest to build, universally useful
2. Listen Mode — we already have the tools (tts + web_fetch)
3. Voice Journal — Whisper API skill exists, just needs the workflow
4. Morning Brief (Personal) — adapt existing work pattern

**Tier 2 (Ship second — higher value, more complexity):**
5. Content Optimization — core to personal brand promise
6. Cross-Platform Repurposing — enhances existing content engine
7. Reply Engagement — `x-reply-guy` already designed

**Tier 3 (Evaluate — useful but complex or sensitive):**
8. Wallet Tracker — many existing alternatives
9. Health Tracker — privacy sensitive, niche
10. Subscription Tracker — requires email access (security policy conflict)
11. Audience Dashboard — many existing tools

---

*This inventory feeds into FRD-C5 (Personal Persona). Tier 1 items become the MVP scope.*
