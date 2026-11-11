---
name: gtm-report
description: Period digest of what Tapistro did — accounts and contacts in, signals, journey throughput, activations by destination, errors, and credit usage — compared with the previous period, with the few things that need attention. Use for "weekly report", "what happened this month", "Tapistro summary for my team", "credit usage", or "how much did journey X cost".
argument-hint: "[period, e.g. 'last week'] [journey or destination]"
---

# GTM report

Input: $ARGUMENTS

1. **Periods.** The requested period (default: last full week, Monday to Sunday) and the one before it, as ISO-8601 bounds from today's date.
2. **Totals.** `get_summary_report` for funnel stages and per source, destination, journey and persona counts. `report_accounts`, `report_signals` and `report_activations` with a period filter and a small `limit` give `_page.total` for each period.
3. **Journeys.** `journey_funnel` for the period; `list_journey_runs` for failed runs.
4. **Activations.** `report_activations` for the period, grouped by `destinationId` (names via `list_destinations`), with the `error EXISTS` share.
5. **Usage.** `get_usage` with `groupBy: "Journey"` for cost per journey, and with no groupBy for the total.

Format, one screen:
- **Headline:** 2–3 numbers that moved most, with the change versus the previous period.
- **Table:** metric · this period · previous · change.
- **Journeys:** top journeys by activations, and any with failed runs or a sharp drop.
- **Spend:** total credits and the top 3 journeys by cost.
- **Needs attention:** at most 3 items, each with its fix or the skill to run (journey-health, in-market-accounts).

Report numbers as the tools return them; do not estimate missing ones. Say which metrics were unavailable.
