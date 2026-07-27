# Content draft — "A repo can commit daily and still rot" (Clawrari v0.5)

- **Date:** 2026-07-27
- **Status:** draft, gated CLEAN (AWDS v0.5, 0 P0 across both pieces), not yet published
- **Story:** the operator's agent audited its own OSS sync pipeline, found 3 months of silent drift behind a green commit cadence, built a drift-audit manifest, and cut Clawrari v0.5 the same afternoon.
- **Receipts:** clawrari v0.5.0 tag; public model playbook stale 2026-04-19 → refreshed; 39 unported skills found, 4 ported (cross-review, night-work, coding-agent, loop-watcher); drift-audit manifest wired into the every-2-day sync cron (one backlog item per cycle).

---

## X post

My agent's open-source repo was landing a commit every 2 days and still fell 3 months behind.

Nothing crashed. Changelog current, green checks, steady cadence. But the public copy of my model playbook was frozen in April while the private one kept evolving, and 39 skills never made it over at all. [observed]

Root cause: the sync cron only looked at the last 48 hours. No backlog, no memory. Anything it missed once, it missed forever.

The fix was a manifest, not a bigger window: hash-diff every public artifact against the private original, keep a persistent list of what was never ported, make the cron burn one item per cycle.

Found it, built the fix, and cut v0.5 the same afternoon. The repo went from 3 skills to 7.

A repo can commit daily and still rot.

---

## LinkedIn post

My agent runs an open-source project. This week I learned its repo can look healthy and still rot.

The setup: my OpenClaw workspace distills what it learns into Clawrari, a public reference repo. A cron lands a commit every 2 days. Changelog current, green checks, steady cadence. By every visible signal, alive.

Then I diffed the public repo against the private workspace it mirrors:
- the public model playbook hadn't moved since April 19
- 39 skills built since then were never ported
- previously published artifacts had quietly drifted from their originals

Root cause was boring: the sync job only looked at the last 48 hours of changes. No backlog, no memory. Anything it missed once, it missed forever. New lessons flowed while standing drift compounded underneath.

The fix:
- a manifest that hash-diffs every public artifact against its private original
- a persistent backlog of what was never ported, with recorded skip decisions
- the cron burns exactly one backlog item per cycle, on top of its normal work

Same afternoon: model playbook refreshed, 4 flagship skills ported, v0.5 tagged.

The lesson generalizes past agents. Cadence metrics measure motion, not sync. Anything that mirrors something else needs a periodic diff against the source of truth, not a green dashboard.
