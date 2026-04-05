# Slack Workspace Configuration

> **Instructions:** Fill in the sections below with your Slack workspace details.
> This file is loaded at triage time to determine channel priorities and filtering rules.

## Workspace

- **Workspace name:** Your Company (fill in)
- **Your Slack display name:** (fill in)
- **Your Slack user ID:** (fill in — find via profile → More → Copy member ID)

## Channel Priority Tiers

### Priority Channels (Tier 1 when action-oriented)

Channels where messages are likely to need your attention or reply.

| Channel | Why it's priority |
|---------|------------------|
| #your-team | Your direct team |
| <!-- add more --> | |

### Watch Channels (Tier 2 — review)

Channels you want to stay aware of but doesn't always need to act on.

| Channel | Why it's watched |
|---------|-----------------|
| #announcements | Company-wide updates |
| <!-- add more --> | |

### Muted / Noise Channels (Tier 3)

Channels to summarize as counts only, never promote to Tier 1.

| Channel | Category |
|---------|----------|
| #random | Social |
| #pets | Social |
| <!-- add more --> | |

## Key People

People whose DMs or @mentions always get Tier 1 treatment (beyond the family-assistant contacts).

| Name | Role | Slack Handle |
|------|------|-------------|
| <!-- e.g., your manager --> | | |
| <!-- e.g., direct report --> | | |

## Bot Filters

Bots whose messages should be classified as Tier 2 (informational) rather than Tier 3 (noise).

| Bot Name | What it posts | Default Tier |
|----------|--------------|-------------|
| GitHub | PR reviews, CI results | Tier 2 |
| <!-- add more --> | | |

## Custom Rules

Any workspace-specific rules for triage:

- <!-- e.g., "Any message in #incidents is always Tier 1" -->
- <!-- e.g., "Messages from @ceo are always Tier 1" -->
