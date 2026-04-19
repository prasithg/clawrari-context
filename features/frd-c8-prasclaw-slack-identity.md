# FRD-C8: PrasClaw Slack Identity — Feature Brief

_Parent: Clawrari Feature Pipeline_
**Status:** Queued for FRD authoring
**Priority:** High (daily-use feature, Clawrari showcase piece)
**Date:** 2026-04-09

## Problem

All Claw-initiated Slack messages (morning briefings, responsive checks, research replies, daily digests, RLHF thread responses) post as "Prasith Govin." There's no visual distinction between human and AI messages. Thread conversations look like talking to yourself. No presence, no identity, no demo-ability.

## Proposed Solution

Create a dedicated Slack app ("PrasClaw") with its own bot identity — name, avatar, and bot token. This gives Claw a visible, distinct presence in Slack as a team member.

## Key Design Decisions (to explore in FRD)

1. **Dedicated channel** — A #prasclaw or #claw channel for direct back-and-forth (replaces current #prasclaw-briefings pattern?)
2. **Channel-specific behaviors** — Opinionated about what PrasClaw does in each channel (e.g., read-only in #team-leadership, active responder in #prasclaw, briefing-poster in #general)
3. **Dual token architecture** — Bot token for all Claw-initiated messages, user token retained for read-only monitoring of channels where a bot member would be inappropriate
4. **Identity design** — Name, avatar, status messages, activity presence
5. **RLHF flow** — How does the feedback loop change when PrasClaw is a distinct sender? Emoji reactions, thread replies, direct commands?
6. **Generalization for Clawrari** — This is the pattern for "give your AI assistant a Slack identity." How do we package it so any Clawrari user can do this in 15 minutes?

## Technical Foundation

- Slack app creation at api.slack.com (no code, no hosting)
- Bot scopes: chat:write, reactions:write, channels:history/read, groups:history/read, users:read, files:read
- xoxb- bot token plugged into OpenClaw as second Slack channel config
- `/invite @PrasClaw` to relevant channels
- OpenClaw Slack skill already handles the integration — just different token

## Clawrari Angle

This is a first-class Clawrari feature:
- "Give your AI assistant a real identity in your team's Slack"
- Step-by-step setup guide
- Opinionated defaults for channel behaviors
- Template for SOUL.md → Slack persona mapping
- Content piece: "Why your AI assistant needs its own Slack identity (and how to set it up in 15 minutes)"

## References

- Current setup: `slk` CLI with Prasith's user token
- OpenClaw Slack skill: `~/.openclaw/workspace/skills/slack/SKILL.md`
- Existing channel: #prasclaw-briefings (current RLHF + briefing channel)
