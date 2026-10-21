---
name: account-tiering
description: Implement ICP scoring and territory planning as a Tapistro journey — score every account for fit and timing with AI Agents, tier it A/B/C, give each tier its own treatment (deep personal outreach, scaled sequences, ads or nurture), and split tier-A accounts fairly across reps. Use when asked to score, tier, prioritise or segment accounts, build a lead-scoring model, plan territories, or route accounts to reps.
argument-hint: "[account source or segment] [reps or destinations]"
allowed-tools: mcp__plugin_tapistro_tapistro__list_journeys, mcp__plugin_tapistro_tapistro__get_journey, mcp__plugin_tapistro_tapistro__create_journey, mcp__plugin_tapistro_tapistro__update_journey, mcp__plugin_tapistro_tapistro__delete_journey, mcp__plugin_tapistro_tapistro__list_sources, mcp__plugin_tapistro_tapistro__get_source, mcp__plugin_tapistro_tapistro__create_source, mcp__plugin_tapistro_tapistro__update_source, mcp__plugin_tapistro_tapistro__delete_source, mcp__plugin_tapistro_tapistro__list_destinations, mcp__plugin_tapistro_tapistro__get_destination, mcp__plugin_tapistro_tapistro__create_destination, mcp__plugin_tapistro_tapistro__update_destination, mcp__plugin_tapistro_tapistro__delete_destination, mcp__plugin_tapistro_tapistro__list_connections, mcp__plugin_tapistro_tapistro__get_connection, mcp__plugin_tapistro_tapistro__list_personas, mcp__plugin_tapistro_tapistro__get_persona, mcp__plugin_tapistro_tapistro__create_persona, mcp__plugin_tapistro_tapistro__update_persona, mcp__plugin_tapistro_tapistro__delete_persona, mcp__plugin_tapistro_tapistro__list_aiAgents, mcp__plugin_tapistro_tapistro__get_aiAgent, mcp__plugin_tapistro_tapistro__create_aiAgent, mcp__plugin_tapistro_tapistro__update_aiAgent, mcp__plugin_tapistro_tapistro__delete_aiAgent, mcp__plugin_tapistro_tapistro__get_settings, mcp__plugin_tapistro_tapistro__update_settings, mcp__plugin_tapistro_tapistro__list_plays, mcp__plugin_tapistro_tapistro__get_play, mcp__plugin_tapistro_tapistro__create_play, mcp__plugin_tapistro_tapistro__update_play, mcp__plugin_tapistro_tapistro__delete_play, mcp__plugin_tapistro_tapistro__list_brandContexts, mcp__plugin_tapistro_tapistro__get_brandContext, mcp__plugin_tapistro_tapistro__create_brandContext, mcp__plugin_tapistro_tapistro__update_brandContext, mcp__plugin_tapistro_tapistro__delete_brandContext, mcp__plugin_tapistro_tapistro__report_accounts, mcp__plugin_tapistro_tapistro__report_account_by_id, mcp__plugin_tapistro_tapistro__report_persons, mcp__plugin_tapistro_tapistro__report_person_by_id, mcp__plugin_tapistro_tapistro__report_signals, mcp__plugin_tapistro_tapistro__report_activations, mcp__plugin_tapistro_tapistro__journey_funnel, mcp__plugin_tapistro_tapistro__journey_step_events, mcp__plugin_tapistro_tapistro__list_field_catalog, mcp__plugin_tapistro_tapistro__get_field_catalog_entry, mcp__plugin_tapistro_tapistro__get_preview, mcp__plugin_tapistro_tapistro__cancel_preview, mcp__plugin_tapistro_tapistro__preview_source, mcp__plugin_tapistro_tapistro__preview_persona, mcp__plugin_tapistro_tapistro__preview_aiAgent, mcp__plugin_tapistro_tapistro__list_versions, mcp__plugin_tapistro_tapistro__get_version, mcp__plugin_tapistro_tapistro__list_journey_runs, mcp__plugin_tapistro_tapistro__get_journey_run, mcp__plugin_tapistro_tapistro__run_journey, mcp__plugin_tapistro_tapistro__rerun_journey_from_step, mcp__plugin_tapistro_tapistro__cancel_journey_run, mcp__plugin_tapistro_tapistro__list_source_files, mcp__plugin_tapistro_tapistro__lookup, mcp__plugin_tapistro_tapistro__build_expression_ai, mcp__plugin_tapistro_tapistro__get_usage, mcp__plugin_tapistro_tapistro__get_summary_report, mcp__plugin_tapistro_tapistro__list_building_blocks, mcp__plugin_tapistro_tapistro__add_journey_to_play, mcp__plugin_tapistro_tapistro__remove_journey_from_play, mcp__plugin_tapistro_tapistro__get_plays_for_journey, mcp__plugin_tapistro_tapistro__add_brand_context_content, mcp__plugin_tapistro_tapistro__update_brand_context_content, mcp__plugin_tapistro_tapistro__delete_brand_context_content, mcp__plugin_tapistro_tapistro__derive_brand_context, mcp__plugin_tapistro_tapistro__list_recipes, mcp__plugin_tapistro_tapistro__get_recipe, mcp__plugin_tapistro_tapistro__render_recipe
---

# Account tiering

Input: $ARGUMENTS

The strategy: effort should follow value. A small set of best-fit accounts with a reason to buy now deserves people's time; the long tail deserves automation or nothing. Scoring is only useful if each tier then gets different treatment, so in Tapistro the score and the routing live in the same journey.

Step types: `../build-journey/references/steps.md`. Writing agents: the ai-agent-builder skill.

## 1. Define the score with the user

- **Fit** comes from the brand context's ICP: the 3–5 attributes that separate the best customers from the worst (industry, size, region, tech, business model). Check each exists as a field (`list_field_catalog`); missing ones need an enrichment step or a research agent.
- **Timing** comes from signals: which `normalizedSignalType`s, over what window (`report_signals` shows what the tenant has).
- **Tiers**: what A, B and C each get. Typical: A → named rep, personal outreach; B → automated sequence; C → ads audience or nothing.

Prefer an `expression` AI Agent for fit when the attributes are plain fields (exact, free); use `classification` with citations when fit needs judgement or web research.

## 2. Blueprint

```
SourceIngest                      the account universe (CRM list, account search, file)
  → AccountProfileEnrichment      only the fields the score needs, with refreshDurationInDays
  → AccountProfileSignalEnrichment  timing signals, if they come from a separate source
  → Tap AI                        fit agent + timing agent (or one tier agent reading both)
  → Decision                      A · B · C on the agents' values (default: C)
      A → Distribution            buckets per rep, share by capacity
            → Destination per rep (CRM owner assignment or rep-specific list)
      B → PersonSearch → PersonEmailValidation → Destination (sequencer)
      C → Destination (ads audience) or stop
```

Why each piece:
- **Enrich before scoring, and only what the score reads**, with a refresh window so re-runs reuse data instead of paying again.
- **A Decision, not a Filter**: every account lands in exactly one tier, and the default branch catches anything the conditions miss.
- **Distribution for tier A** keeps rep load balanced; use an `AssignField` step instead when ownership follows rules (territory, industry).
- **Top N** before Distribution when reps can take only so many new accounts per run.

## 3. Calibrate before routing anything

- `preview_aiAgent` the fit and timing agents on 10 accounts the user can judge: known customers (should score A), known bad fits (C), a few unknowns. Adjust weights and labels until the user agrees.
- `report_accounts` with the agent fields over the whole source (after a small `maxElements` run) to check the tier sizes; if A is more than reps can work, raise its bar.

## 4. Create, run, measure

Follow build-journey for creation and approval; add it to a play. Measure per tier with `journey_funnel` and `report_activations`, and from engagement signals (`email_replied_positive`, `meeting_booked`) by tier. If B converts like A, the bar between them is in the wrong place.
