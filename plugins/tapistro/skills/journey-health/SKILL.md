---
name: journey-health
description: Diagnose a Tapistro journey — where records drop off, failed or stuck runs, activation errors, and why a record never reached its CRM, ad platform or sequencer — and propose the fix, including re-running from the step that was fixed. Use when asked how a journey is doing, why numbers are low, why an account or contact was not pushed, or about destination or activation errors.
argument-hint: "[journey, destination, account or contact] [period]"
---

# Journey health

Input: $ARGUMENTS

Find the single biggest problem first, prove it with numbers, then fix it.

1. **Scope.** Resolve the journey (`list_journeys`, ask if several match), or start from the record or destination named: `report_accounts` by `domain`, `report_persons` by `emailId`, `list_destinations`. Default period: last 7 days, and say so.
2. **Graph.** `get_journey` so step ids map to names and order (follow `edges` from → to).
3. **Runs.** `list_journey_runs`; `get_journey_run` on the latest and any failed or long-running one for per-step counts and errors. A failed run explains everything downstream of it.
4. **Funnel.** `journey_funnel` for the journey and period. Compute drop % per step. A large drop at a filter or AI Agent step is often correct; check it against intent before calling it a bug.
5. **Drill in.** `journey_step_events` at the worst step for sample records. For an AI Agent step, `get_aiAgent` and compare its labels with the samples. `list_versions` shows whether a step's agent or the journey changed recently.
6. **Activations.** `report_activations` with `journeyId` (or `destinationId`, `accountId`, `contactId`) and `error EXISTS`; fields `["timestamp","destinationId","error","account.name"]`. Group by error message.
7. **Destination.** For auth or permission errors, `get_destination` and `get_connection`: the connection usually needs re-authorising in the Tapistro UI.

Report: root cause in one line, the evidence (funnel table step → in → out → drop %, top errors with counts, sample ids), and the fix.
Fixing is a separate, approved step:
- Config fixes go through `update_*` with the full object.
- After fixing a step, offer `rerun_journey_from_step` from that step (`rerunErrorsOnly` for only the failed records) rather than `run_journey`, which pulls fresh records and leaves the stuck ones behind.

Never change or run anything without the user's explicit go-ahead.
