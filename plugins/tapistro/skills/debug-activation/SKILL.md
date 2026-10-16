---
name: debug-activation
description: Diagnose failed or missing Tapistro activations — records that did not reach a CRM, ad platform, or sequencer. Use when asked why an account or contact was not pushed, or why a destination shows errors.
argument-hint: "[destination, journey, account or contact]"
allowed-tools: mcp__plugin_tapistro_tapistro__report_activations, mcp__plugin_tapistro_tapistro__report_accounts, mcp__plugin_tapistro_tapistro__report_account_by_id, mcp__plugin_tapistro_tapistro__report_persons, mcp__plugin_tapistro_tapistro__report_person_by_id, mcp__plugin_tapistro_tapistro__list_destinations, mcp__plugin_tapistro_tapistro__get_destination, mcp__plugin_tapistro_tapistro__get_connection, mcp__plugin_tapistro_tapistro__get_journey, mcp__plugin_tapistro_tapistro__list_journey_jobs, mcp__plugin_tapistro_tapistro__get_journey_job, mcp__plugin_tapistro_tapistro__journey_step_events_summary_by_journey
---

# Debug activations

Input: $ARGUMENTS

1. Scope it: resolve the destination (`list_destinations`), journey, account (`report_accounts` by `domain`), or contact (`report_persons` by `emailId`).
2. Errors: `report_activations` with fields `["timestamp","destinationId","journeyId","accountId","contactId","error"]` and `error EXISTS`, plus the scope filters. Group by error message.
3. No activation at all: check the record reached the activation step — `journey_step_events_summary_by_journey` and `get_journey` for the step order — and that recent runs succeeded (`list_journey_jobs`, `get_journey_job`).
4. Destination side: `get_destination` for its config and `get_connection` for its connection. Auth or permission errors there usually mean the connection must be re-authorized in Tapistro.

Report: the root cause in one line, the evidence (counts, sample error, ids), and the fix. Do not edit destinations, connections, or journeys without the user's explicit go-ahead.
