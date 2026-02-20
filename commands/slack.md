---
description: Triage Slack messages — prioritized summary with reply drafting
---

Invoke the `slack-triage` skill to scan Eric's Slack workspace and produce a prioritized triage.

1. Load the slack-triage skill
2. Execute the full triage workflow (scan → classify → present)
3. Present results in the standard three-tier format
4. Offer reply drafting for Tier 1 items

If the user provides an argument, filter accordingly:
- `$ARGUMENTS` = "dms" → only direct messages
- `$ARGUMENTS` = "mentions" → only messages where Eric is @mentioned
- `$ARGUMENTS` = "threads" → only threads Eric is participating in
- `$ARGUMENTS` = "work" or "logitech" → only work-related channels
- No argument → full workspace triage
