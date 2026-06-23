# FRD-C8: Assistant Slack Identity — Feature Brief

_Parent: Clawrari Feature Pipeline_
**Status:** Queued for FRD authoring
**Priority:** High (daily-use feature, Clawrari showcase piece)
**Date:** 2026-04-09

## Problem

When an assistant posts through a human account, messages are visually ambiguous. Briefings, research replies, feedback threads, and operational digests can look like the human is talking to themselves. That weakens trust, reviewability, and demo quality.

## Proposed Solution

Create a dedicated Slack app for the assistant with its own bot identity: name, avatar, bot token, channel membership, and behavior policy. The assistant becomes a visible team member instead of borrowing a human identity.

## Key Design Decisions

1. **Dedicated channel** — A named assistant channel for direct back-and-forth, corrections, and briefings.
2. **Channel-specific behaviors** — Read-only in sensitive channels, active responder in its own channel, briefing poster where explicitly configured.
3. **Dual-token boundary** — Bot token for assistant-initiated messages; user-level access only where read-only monitoring is explicitly needed and approved.
4. **Identity design** — Name, avatar, status messages, and activity presence.
5. **Feedback flow** — Emoji reactions, thread replies, and direct commands as reusable correction signals.
6. **Generalization for Clawrari** — Package the pattern so any Clawrari user can give their assistant a Slack identity in minutes.

## Technical Foundation

- Slack app creation at api.slack.com.
- Bot scopes such as `chat:write`, `reactions:write`, channel history/read scopes, `users:read`, and `files:read`, selected by least privilege.
- Bot token configured as a separate Slack channel/integration in the assistant runtime.
- Explicit `/invite` into only the channels where the assistant should operate.
- Existing Slack skill/integration handles posting; this feature adds identity and policy boundaries.

## Clawrari Angle

This is a first-class Clawrari feature:

- “Give your AI assistant a real identity in your team’s Slack.”
- Step-by-step setup guide.
- Opinionated defaults for channel behaviors.
- Template for persona/configuration to Slack identity mapping.
- Content piece: “Why your AI assistant needs its own Slack identity — and how to set it up safely.”

## References

- Current setup pattern: Slack CLI/app credentials configured locally by the user.
- Slack integration docs for the assistant runtime.
- Dedicated assistant feedback/briefing channel pattern.
