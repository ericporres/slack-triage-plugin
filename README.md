# Slack Triage Plugin for Claude

A plugin template that turns Claude into your Slack triage assistant — one who knows which messages need a reply, which threads need a glance, and which 200 channel messages are noise you can skip entirely.

## What This Is

This is a **plugin** for [Claude](https://claude.ai) (works with both [Cowork](https://claude.ai) and [Claude Code](https://docs.claude.com/en/docs/claude-code)). Once configured, you can say "check my Slack" and Claude will:

- **Scan your workspace** using the Slack MCP connector (DMs, mentions, threads, priority channels)
- **Classify every message** into three tiers: Reply Needed, Review, and Noise
- **Draft replies** for the urgent ones, matching your Slack voice (more casual than email)
- **Summarize channel activity** so you know what happened without reading 400 messages

The triage handles the morning catch-up problem: you open Slack, see 47 unread channels, and spend 30 minutes scrolling before you've actually *done* anything. This gives you a prioritized action list instead.

## Why This Approach

Most Slack notification management relies on mute/unmute decisions per channel. That's a binary choice applied to a nuanced problem. A channel might be 95% noise but 5% critical — you can't mute it, but you also can't read everything.

This takes a different approach: Claude reads your Slack activity (DMs, mentions, threads, channel messages) and makes judgment calls the way a good chief of staff would. It knows which people and channels matter most, which threads you started and haven't closed, and which bot notifications are informational vs. ignorable.

The key insight is **channel-priority mapping**. You define which channels are priority (team channels, incident channels), which are watch-only (announcements, project updates), and which are noise (social, random). Combined with people-priority (your manager, direct reports, key stakeholders), 80% of messages get classified instantly.

This pairs well with the [Email Triage Plugin](https://github.com/ericporres/email-triage-plugin) for a complete morning briefing, and the [Family Assistant Skill](https://github.com/ericporres/family-assistant-skill) for contact context. But it works standalone too.

## Getting Started

### 1. Install the plugin

**For Cowork users:**
Drop the plugin folder into whichever folder you select when starting a Cowork session.

**For Claude Code users:**
Copy it into your project or home directory:

```bash
cp -r slack-triage-plugin ~/.claude/plugins/slack-triage-plugin
```

### 2. Connect Slack

This plugin requires the Slack MCP connector. In Cowork, enable the Slack connector from the connectors menu. In Claude Code, configure the Slack MCP server in your `.claude.json`.

The Slack MCP connector provides: `slack_read_channel`, `slack_search_public_and_private`, `slack_search_users`, and `slack_search_channels`.

### 3. Customize the workspace config

Open `skills/slack-triage/references/workspace-config.md` and fill in:

| Section | What to configure |
|---------|------------------|
| **Workspace** | Your Slack display name and user ID |
| **Priority Channels** | Channels where messages are likely to need your attention |
| **Watch Channels** | Channels you want to stay aware of but rarely act on |
| **Muted Channels** | Channels to summarize as counts only |
| **Key People** | People whose DMs always get Tier 1 treatment |
| **Bot Filters** | Bots whose notifications are Tier 2 (informational) vs. Tier 3 (noise) |

The most impactful customization is the channel priority table. Start with your top 5 channels and your manager/direct reports.

### 4. Start using it

```
/slack              → Full workspace triage
/slack dms          → Only direct messages
/slack mentions     → Only @mentions
/slack threads      → Only threads you're in
/slack work         → Only work-related channels
/messages           → Alias for /slack
```

Or just say:
- *"Check my Slack"*
- *"What did I miss on Slack?"*
- *"Catch me up on messages"*
- *"Draft a reply to #3"*
- *"Summarize what happened this week"*

## File Structure

```
slack-triage-plugin/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── .mcp.json                # Slack MCP connector config
├── commands/
│   ├── slack.md             # /slack slash command
│   └── messages.md          # /messages alias
├── skills/
│   └── slack-triage/
│       ├── SKILL.md         # Triage logic, tier definitions, voice guidelines
│       └── references/
│           └── workspace-config.md  # Your channel/people priority config
├── LICENSE
└── README.md
```

## How the Three Tiers Work

**Tier 1 — Reply Needed.** Someone is waiting on you. Direct messages, @mentions with questions, threads you started with unresolved asks, escalations, and messages from priority people. These are numbered so you can say "draft a reply to #2" and Claude will pull up the full thread context and draft something in your Slack voice.

**Tier 2 — Review.** Needs your eyes but not a reply. FYI mentions, shared documents, announcements in watched channels, bot notifications (deploy alerts, CI results), and reactions on your messages. Presented as a quick-scan list.

**Tier 3 — Noise.** General channel chatter, social messages, automated bot spam. Summarized as counts by channel ("34 in #random, 12 in #social, 8 bot notifications").

## Tips

**Configure channel priorities.** This is the single biggest upgrade. Five minutes mapping your channels to priority tiers will make every future triage dramatically better.

**Pair with the Email Triage Plugin.** Run `/email` then `/slack` for a complete morning briefing. Same three-tier pattern, same mental model, two different communication channels covered.

**Use the catch-up mode.** Back from vacation? Say "catch me up on Slack this week" and Claude extends the time window and adds a "decisions made without you" section.

**Thread replies over channel messages.** When Claude drafts a reply, it defaults to thread replies to keep channels clean. This matches Slack best practices.

## What It Doesn't Do

- **Never sends without confirmation.** Claude drafts replies and waits for your explicit "send it."
- **Never joins or leaves channels.** Read-only triage.
- **Never modifies pins, bookmarks, or channel settings.** Observation only.
- **Never auto-marks as read.** Your unread state is yours to manage.

## Companion Templates

- [Email Triage Plugin](https://github.com/ericporres/email-triage-plugin) — Same three-tier pattern for Gmail
- [Family Assistant Skill](https://github.com/ericporres/family-assistant-skill) — Contact context that makes triage smarter

## Background

This template was built by [Eric Porres](https://github.com/ericporres) as a companion to the email triage plugin. The same three-tier pattern that works for 200 emails a day works just as well for 400 Slack messages — the classification engine is the same, the communication medium just has different signals (channels vs. aliases, threads vs. email chains, reactions vs. read receipts).

## License

MIT — use it however you'd like.
