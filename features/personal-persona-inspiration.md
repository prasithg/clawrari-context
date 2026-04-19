# Personal Persona — Feature Inspiration

## Source: "How I Use OpenClaw for My Personal Life" (YouTube)
URL: https://youtu.be/V5UrO79XkNc
Added: 2026-04-08

7 personal automation use cases, all via Telegram topic-per-use-case pattern:

1. **Voice Journal** — Voice messages → transcription → daily markdown file → end-of-day highlights + tomorrow's focus areas
2. **Wallet Tracker** — Voice/text expense+income logging → balance tracking → monthly spending summaries
3. **Morning Brief** — Weather + overnight AI news + calendar events + to-dos (we already have this)
4. **Subscription Tracker** — Scans emails for invoices/payments → monitors recurring charges
5. **Listen Mode** — Drop text/article links → agent converts to audio for playback (TTS)
6. **Health Tracker** — Log meals/symptoms → markdown → reminders (morning/midday/evening) → weekly pattern analysis (food-symptom correlations, triggers) every Sunday
7. **Habit Tracker** — Define habits + check-in schedule + tracking tone → weekly Sunday reports with completion rates, streaks, best/worst days

### What We Already Have vs. What's New
| Feature | Our Status | Notes |
|---------|-----------|-------|
| Morning Brief | ✅ Have it | Ours is richer (Slack, calendar, responsive checks) |
| Voice Journal | ❌ New | Would need Whisper transcription + daily file pattern |
| Wallet Tracker | ❌ New | Could integrate with Google Sheets for persistence |
| Subscription Tracker | ⚠️ Partial | Email access disabled for security — needs rethink |
| Listen Mode | ⚠️ Partial | We have TTS skill — need the "drop article → audio" workflow |
| Health Tracker | ❌ New | Interesting for personal persona — meals, symptoms, patterns |
| Habit Tracker | ❌ New | Strong personal persona feature — streaks, accountability |

### Key Pattern: Telegram Topic-Per-Use-Case
The video uses one Telegram group with multiple topics, each dedicated to a use case. This is a clean UX pattern for the personal persona — separate contexts within one channel.

### Priority for Clawrari Personal Persona
- **P1 (high fit):** Voice Journal, Habit Tracker, Listen Mode
- **P2 (nice to have):** Wallet Tracker, Health Tracker
- **P3 (blocked/complex):** Subscription Tracker (email security concerns)
