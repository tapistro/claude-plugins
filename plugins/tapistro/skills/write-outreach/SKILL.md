---
name: write-outreach
description: Draft a first-touch cold email or LinkedIn message to a specific contact, grounded in Tapistro data (recent signals, AI Agent verdicts, the account's profile) and the company's brand context and voice. Short, specific, never invents facts. Use when asked to write an email, opener or LinkedIn note to someone at an account, or draft outreach for a list of accounts. Not for sequences, follow-ups or replies to warm threads.
argument-hint: "[contact email or name @ domain] [channel: email | linkedin]"
---

# Write outreach

Target: $ARGUMENTS

## Gather

1. Contact and account: `report_persons` (by `emailId`, or `accountId` + name) and `report_account_by_id`, including the `computed:<id>` fields from `list_aiAgents`.
2. Recent signals for the account (`report_signals`, last 30 days).
3. Brand context (`list_brandContexts` → `get_brandContext`): what we sell, the problem it solves for this contact's role, proof points, and voice.

## The observation comes from data

The opening line is one specific, current fact about them: a signal (visited the pricing page, researching a topic), a hire, a launch, an AI Agent finding with its citation. If the data has nothing specific, say so and ask the user for one. Never invent or embellish facts about a real company or person, and never reveal tracking ("I saw you on our pricing page"); describe the underlying need instead.

## Write

Lead with that fact, connect it to a problem this person's role owns (from the brand context), and end with an easy question. Keep it short enough to read on a phone, in the brand's voice, about them rather than us. If the message could be sent unchanged to someone else, the fact is not specific enough.

Return the subject and body, then the angle in one sentence and the data points used with their dates. For several contacts, one draft each and a table of angles.

## From one draft to a journey

Hand-drafting suits a few accounts. When the user approves the voice, offer to turn it into a content AI Agent (ai-agent-builder) whose instruction encodes the angle and the approved draft as the example, so a journey writes the same quality for every contact; multichannel-sequence and signal-triggered-outreach show where it fits.

Drafts only. Sending happens through the user's destination or sequencer; ask before pushing anything there.
