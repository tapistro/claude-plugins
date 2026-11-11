---
name: in-market-accounts
description: Find and rank the accounts (or contacts) showing buying intent in Tapistro — web visits, intent topics, other signals — weighted by recency, signal strength and AI Agent fit, flag the ones not yet activated, and offer the next action. Use for "who's in market", "who visited this week", "top accounts by intent", "hot accounts", or signal activity for a domain.
argument-hint: "[period] [source, signal type, segment or domain]"
---

# In-market accounts

Input: $ARGUMENTS

1. **Window.** Turn the period into ISO-8601 bounds from today's date. Default: last 7 days, and say so.
2. **Signals.** `report_signals` filtered on `timestamp` (plus `source` / `normalizedSignalType` if asked), fields `["timestamp","source","normalizedSignalType","accountId","account.name","account.domain"]`. Page with `cursor` until done or you have a few hundred rows; say if you stopped early.
3. **Fit.** `list_aiAgents` for fit, tier or ICP agents; their values are account fields named `computed:<id>`. Fetch them with `report_accounts` (filter `id` on the signalled accounts, `\u0000`-separated for IN) together with `name`, `domain`, `stage`.
4. **Rank.** Per account: signal count, distinct signal types, most recent signal, fit. Weight recent, repeated and high-intent signals (pricing or demo pages, intent topics) above a single generic visit; put poor-fit accounts last however active they are. State the weighting in one line so the user can change it.
5. **Coverage.** `stage` (or `report_activations` by `accountId`) shows which top accounts were already activated. Unactivated high-fit, high-intent accounts are the headline.

Answer with a table: rank, account, domain, signals (count · types · latest), fit, activated? Then one line on the pattern (e.g. "8 of the top 10 hit pricing; 5 are not in any journey").

Next actions to offer, each needing the user's go-ahead: brief an account (account-brief), draft outreach (write-outreach), or route the segment into a journey (build-journey).
