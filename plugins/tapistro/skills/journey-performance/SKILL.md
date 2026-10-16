---
name: journey-performance
description: Review how a Tapistro journey (or all journeys) performed — step funnel counts, recent runs, and activation failures. Use when asked how a journey is doing, where accounts drop off, or why a journey activated fewer records than expected.
argument-hint: "[journey name or id] [period, e.g. 'last 7 days']"
allowed-tools: mcp__plugin_tapistro_tapistro__list_journeys, mcp__plugin_tapistro_tapistro__get_journey, mcp__plugin_tapistro_tapistro__journey_step_events_summary, mcp__plugin_tapistro_tapistro__journey_step_events_summary_by_journey, mcp__plugin_tapistro_tapistro__journey_step_events, mcp__plugin_tapistro_tapistro__list_journey_jobs, mcp__plugin_tapistro_tapistro__get_journey_job, mcp__plugin_tapistro_tapistro__report_activations
---

# Journey performance review

Input: $ARGUMENTS

1. Resolve the journey. If given a name, `list_journeys` with `query`; if several match, ask which one. With no journey given, review all journeys.
2. Load its graph with `get_journey` so step ids map to step names and order (follow `edges` from → to).
3. Funnel:
   - One journey: `journey_step_events_summary_by_journey`.
   - All journeys or a period: `journey_step_events_summary` with a `timestamp >=` filter (ISO-8601, computed from today's date) and, if scoped, a `journeyId` filter.
4. Runs: `list_journey_jobs` (raise `runsPerJourney` to see a trend); `get_journey_job` for a failed or stuck run.
5. Activation failures: `report_activations` with fields `["timestamp","destinationId","error","account.name"]` and filters `journeyId = <id>` and `error EXISTS`.
6. Drill into a step with an unexpected drop using `journey_step_events` for that step.

Report as a compact table: step → in → out → drop %, then runs (status, time), then the top activation errors grouped by message. Lead with the single biggest drop-off and its likely cause. Do not change the journey; suggest edits and let the user decide.
