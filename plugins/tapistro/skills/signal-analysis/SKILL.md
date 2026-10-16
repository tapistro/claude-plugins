---
name: signal-analysis
description: Analyze Tapistro signals — which companies or contacts showed intent or web visits in a period, from which sources, and which of them were activated. Use for questions like "who visited last week", "top accounts by signals", or "intent signals for acme.com".
argument-hint: "[period] [source, signal type, account or domain]"
allowed-tools: mcp__plugin_tapistro_tapistro__report_accounts, mcp__plugin_tapistro_tapistro__report_account_by_id, mcp__plugin_tapistro_tapistro__report_persons, mcp__plugin_tapistro_tapistro__report_signals, mcp__plugin_tapistro_tapistro__report_activations, mcp__plugin_tapistro_tapistro__list_field_catalog, mcp__plugin_tapistro_tapistro__get_field_catalog_entry, mcp__plugin_tapistro_tapistro__list_sources
---

# Signal analysis

Input: $ARGUMENTS

1. Turn the period into an ISO-8601 start (and end) from today's date. Default to the last 7 days and say so.
2. If the question names a field you are unsure of, find its path with `list_field_catalog` (`profileType` signal or account) before using it.
3. Pick the query by question shape:
   - Which companies: `report_accounts`, fields `["id","name","domain"]`, filter `SIGNALS.timestamp >= <start>` (add `SIGNALS.source` / `SIGNALS.normalizedSignalType` terms to narrow).
   - Which contacts: `report_persons` with the same `SIGNALS.*` filters and `account.name`.
   - Signal details: `report_signals`, fields `["timestamp","source","normalizedSignalType","account.name","account.domain"]`, filter on `timestamp`.
   - Were they activated: add `ACTIVATIONS.timestamp >= <start>` on accounts, or query `report_activations` by `accountId`.
4. Request only the fields the answer needs. Page with `cursor` ("" then `_page.nextCursor`) until you have enough rows or `nextCursor` is absent; state the total from `_page.total` when present.

Answer with a short table (account, domain, signal count or latest signal, source). Note when results were truncated by paging.
