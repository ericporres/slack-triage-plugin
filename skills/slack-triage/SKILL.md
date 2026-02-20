---
name: slack-triage
description: >
  This skill should be used when the user asks to "check Slack", "triage my Slack",
  "check my messages", "Slack summary", "what did I miss on Slack", or invokes /slack or /messages.
  Scans Eric's Slack workspace for recent messages, DMs, threads, and mentions — classifies by
  priority tier, and offers reply drafting. References porres-family-assistant for contacts context.
---

# Slack Triage Skill

## Overview

Scan Eric's Slack workspace for recent messages, classify them into three priority tiers, and offer to draft replies for urgent items. This skill mirrors the email-triage pattern but adapted for Slack's channel-based, threaded communication model.

This skill does NOT maintain its own contact data — it reads from the porres-family-assistant skill as the canonical source for people context.

## Available Slack MCP Tools

The Slack connector (https://mcp.slack.com/mcp) provides these tools:

| Tool | Purpose |
|------|---------|
| `slack_read_channel` | Read recent messages from a specific channel |
| `slack_search_public_and_private` | Search across all accessible channels |
| `slack_search_users` | Find users by name or email |
| `slack_search_channels` | Find channels by name or topic |

## Step 0 — Load Context (runtime references)

Before scanning, read these files from the family assistant to establish priority context:

| File | What it provides |
|------|-----------------|
| `shared/skills/porres-family-assistant/references/family-members.md` | Family names — helps identify personal messages from family members |
| `shared/skills/porres-family-assistant/references/email-aliases.md` | Alias routing — email/Slack identity overlap |

**Load only these two.** Don't load insurance, medical, or finance unless a specific message requires that context.

Also load `references/workspace-config.md` from this skill for channel priority mappings (once Eric configures it).

## Step 1 — Scan Workspace

Use the Slack MCP tools to gather recent activity. Run these searches in parallel:

### 1a. Direct Messages
Search for recent DMs sent to Eric:
```
slack_search_public_and_private: "to:me" (filtered to DMs)
```

### 1b. Mentions
Search for @mentions of Eric across channels:
```
slack_search_public_and_private: "@eric" or Eric's Slack user mention
```

### 1c. Threads Eric is in
Search for replies in threads Eric has participated in:
```
slack_search_public_and_private: threads where Eric has replied
```

### 1d. Priority Channels
Read latest messages from channels marked as priority in `references/workspace-config.md`.

**Time window options:**
- Default: last 24 hours (good for daily morning triage)
- If Eric says "just now" or "last hour": last 2 hours
- If Eric says "catch me up" or "this week": last 3 days

**Snippet-first approach:** Classify from search result snippets first. Only read full channel history or thread for:
- All Tier 1 messages (need full context for reply drafting)
- Ambiguous Tier 2 messages where the snippet isn't enough to classify

This avoids token blowout on busy days.

## Step 2 — Classify into Tiers

### Tier 1 — Reply Needed

Messages with a direct question, request, or action aimed at Eric. Each gets:
- One-line summary of what the sender needs
- Channel/DM context
- Suggested action: Reply / React / Schedule / Forward to [person]

**Urgency signals (auto-promote to Tier 1):**
- Direct messages from anyone (DMs inherently signal intent)
- @mentions of Eric by name
- Messages from Eric's manager or direct reports
- Time-sensitive language: "by EOD", "deadline", "urgent", "asap", "blocker", "blocked on you", "need your input"
- Threads Eric started that have unresolved questions
- Messages in channels tagged as "priority" in workspace config
- Requests with deadlines or action items assigned to Eric
- Escalations or incidents (keywords: "P0", "P1", "incident", "outage", "down", "broken", "escalating")

### Tier 2 — Review / Decide

Needs Eric's eyes but not necessarily a reply:
- FYI mentions ("cc @eric", "looping in @eric")
- Shared links, documents, or announcements in priority channels
- Meeting notes or decision summaries
- Channel activity in watched channels (not priority, but relevant)
- Bot notifications (deploy alerts, CI/CD results, PR reviews)
- Reactions/threads on Eric's own messages (social signals)

### Tier 3 — Noise

General channel chatter, automated bot spam, social messages, memes, off-topic threads. Summarize as counts by channel.

### Channel Priority Mapping

Use `references/workspace-config.md` for Eric's channel configuration. Until configured, use these defaults:

**Default priority channels (Tier 1 if action-oriented):**
- Any channel with "ai", "logitech", or Eric's team name in the name
- #general (company-wide announcements only — filter noise)

**Default review channels (Tier 2):**
- Project-specific channels Eric is a member of
- #announcements or #all-hands type channels

**Default noise (Tier 3):**
- #random, #social, #watercooler, #pets, etc.
- Channels Eric hasn't posted in for 30+ days

## Step 3 — Present Results

Format output as:

```markdown
# Slack Triage — [Today's Date]
[X messages scanned from last 24h across Y channels]

## Reply Needed (X)

1. **[Sender Name]** in #[channel] (or DM)
   [One-line summary of what they need]
   → Suggested: [Reply / React / Schedule / Forward to X]

2. ...

## Review (X)

- **[Sender]** in #[channel] — [One-line summary]
- ...

## Noise (X)
[X] #random, [X] #social, [X] bot notifications, [X] other
```

**Numbering matters.** Tier 1 items are numbered so Eric can say "draft a reply to #3."

## Step 4 — Reply Drafting (conversational)

When Eric asks to draft a reply (e.g., "draft a reply to #3"):

1. Read the full thread for context using `slack_read_channel` or search tools
2. Identify who else is in the thread (for tone calibration)
3. Draft a reply matching Eric's Slack voice
4. Present the draft for Eric's review
5. **Never auto-send.** Always require explicit "send it" or "yes, post" confirmation.

### Eric's Slack voice guidelines
- More casual than email — shorter sentences, fewer formalities
- Direct and to the point
- Uses emoji reactions where appropriate (suggest these too)
- First names always
- Doesn't over-explain
- Thread replies preferred over channel-level responses for ongoing discussions

## Step 5 — Catch-Up Summary (optional)

If Eric asks for a summary rather than full triage:

```markdown
# Slack Catch-Up — [Date Range]

**Hot topics:** [2-3 sentence summary of the most important threads]

**Action items for you:**
1. [Action] — from [Person] in #[channel]
2. ...

**Decisions made without you:**
- [Decision] in #[channel] — [who decided, when]

**Nothing urgent:** [Channels with no activity or only noise]
```

## Edge Cases

- **Slack connector not connected:** Tell Eric the Slack connector isn't available yet and remind him to connect it in Cowork settings. Offer to check email instead.
- **Empty results:** "Nothing new on Slack in the last 24 hours — all quiet."
- **Very high volume (100+ messages):** Warn Eric, offer to filter by DMs-only, mentions-only, or specific channels.
- **Cross-platform threads:** If a Slack message references an email or vice versa, note the cross-reference.
- **Out of office:** If Eric mentions he's been away, auto-extend the time window to cover the absence.

## What This Skill Does NOT Do

- Does not maintain its own contact list (uses family-assistant)
- Does not save output to files (ephemeral conversation view)
- Does not send messages without explicit confirmation
- Does not modify channel settings, pins, or bookmarks
- Does not join or leave channels
- Does not handle Slack Connect (external org) channels differently (yet)
