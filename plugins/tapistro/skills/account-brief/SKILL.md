---
name: account-brief
description: One-page Tapistro brief on a single account — what the company is, why it fits (AI Agent verdicts with their reasoning), what it has been doing (recent signals), who to talk to (key contacts), and what Tapistro already did with it (journeys, activations). Use for "tell me about acme.com", "prep me for a call with X", "what do we know about this account", or before writing outreach.
argument-hint: "[domain or account name]"
---

# Account brief

Account: $ARGUMENTS

1. **Find it.** `report_accounts` filtered on `domain` (or `query` for a name). If several match, ask.
2. **Profile.** `report_account_by_id` with firmographic fields (name, domain, industry, employee count, HQ, description; find paths with `list_field_catalog` if unsure) plus every `computed:<id>` field from `list_aiAgents` that targets accounts.
3. **Activity.** `report_signals` filtered on `accountId`, last 90 days, newest first: what, when, from which source.
4. **People.** `report_persons` filtered on `accountId`: name, title, email status. Mark who the personas selected.
5. **History.** `report_activations` filtered on `accountId`: which journeys and destinations it went to, when, with errors. `get_journey` names the journeys.

Write the brief in this order, a few lines each:
- **Snapshot:** one sentence on what they do, plus size and location.
- **Why now:** the 1–3 strongest recent signals, with dates.
- **Fit:** each AI Agent verdict with its reasoning and citations where present.
- **People:** the 3–5 most relevant contacts and why.
- **Touched so far:** journeys, destinations and dates; anything that errored.
- **Suggested angle:** one line linking "why now" to the problem their buyer owns.

State only what the data shows. Mark gaps ("no signals in 90 days") instead of filling them in.
