---
name: multi-threading
description: Implement buying-committee coverage (multi-threading) as a Tapistro journey — for each target account find people in every buying role (economic buyer, champion, technical evaluator, end user), check which roles are covered, fill the gaps, and send each role a message about its own concern. Use when asked to reach several people per account, map or cover the buying committee, avoid single-threaded deals, or find who else to contact at an account.
argument-hint: "[accounts or journey] [roles]"
allowed-tools: mcp__plugin_tapistro_tapistro__list_journeys, mcp__plugin_tapistro_tapistro__get_journey, mcp__plugin_tapistro_tapistro__create_journey, mcp__plugin_tapistro_tapistro__update_journey, mcp__plugin_tapistro_tapistro__delete_journey, mcp__plugin_tapistro_tapistro__list_sources, mcp__plugin_tapistro_tapistro__get_source, mcp__plugin_tapistro_tapistro__create_source, mcp__plugin_tapistro_tapistro__update_source, mcp__plugin_tapistro_tapistro__delete_source, mcp__plugin_tapistro_tapistro__list_destinations, mcp__plugin_tapistro_tapistro__get_destination, mcp__plugin_tapistro_tapistro__create_destination, mcp__plugin_tapistro_tapistro__update_destination, mcp__plugin_tapistro_tapistro__delete_destination, mcp__plugin_tapistro_tapistro__list_connections, mcp__plugin_tapistro_tapistro__get_connection, mcp__plugin_tapistro_tapistro__list_personas, mcp__plugin_tapistro_tapistro__get_persona, mcp__plugin_tapistro_tapistro__create_persona, mcp__plugin_tapistro_tapistro__update_persona, mcp__plugin_tapistro_tapistro__delete_persona, mcp__plugin_tapistro_tapistro__list_aiAgents, mcp__plugin_tapistro_tapistro__get_aiAgent, mcp__plugin_tapistro_tapistro__create_aiAgent, mcp__plugin_tapistro_tapistro__update_aiAgent, mcp__plugin_tapistro_tapistro__delete_aiAgent, mcp__plugin_tapistro_tapistro__get_settings, mcp__plugin_tapistro_tapistro__update_settings, mcp__plugin_tapistro_tapistro__list_plays, mcp__plugin_tapistro_tapistro__get_play, mcp__plugin_tapistro_tapistro__create_play, mcp__plugin_tapistro_tapistro__update_play, mcp__plugin_tapistro_tapistro__delete_play, mcp__plugin_tapistro_tapistro__list_brandContexts, mcp__plugin_tapistro_tapistro__get_brandContext, mcp__plugin_tapistro_tapistro__create_brandContext, mcp__plugin_tapistro_tapistro__update_brandContext, mcp__plugin_tapistro_tapistro__delete_brandContext, mcp__plugin_tapistro_tapistro__report_accounts, mcp__plugin_tapistro_tapistro__report_account_by_id, mcp__plugin_tapistro_tapistro__report_persons, mcp__plugin_tapistro_tapistro__report_person_by_id, mcp__plugin_tapistro_tapistro__report_signals, mcp__plugin_tapistro_tapistro__report_activations, mcp__plugin_tapistro_tapistro__journey_funnel, mcp__plugin_tapistro_tapistro__journey_step_events, mcp__plugin_tapistro_tapistro__list_field_catalog, mcp__plugin_tapistro_tapistro__get_field_catalog_entry, mcp__plugin_tapistro_tapistro__get_preview, mcp__plugin_tapistro_tapistro__cancel_preview, mcp__plugin_tapistro_tapistro__preview_source, mcp__plugin_tapistro_tapistro__preview_persona, mcp__plugin_tapistro_tapistro__preview_aiAgent, mcp__plugin_tapistro_tapistro__list_versions, mcp__plugin_tapistro_tapistro__get_version, mcp__plugin_tapistro_tapistro__list_journey_runs, mcp__plugin_tapistro_tapistro__get_journey_run, mcp__plugin_tapistro_tapistro__run_journey, mcp__plugin_tapistro_tapistro__rerun_journey_from_step, mcp__plugin_tapistro_tapistro__cancel_journey_run, mcp__plugin_tapistro_tapistro__list_source_files, mcp__plugin_tapistro_tapistro__lookup, mcp__plugin_tapistro_tapistro__build_expression_ai, mcp__plugin_tapistro_tapistro__get_usage, mcp__plugin_tapistro_tapistro__get_summary_report, mcp__plugin_tapistro_tapistro__list_building_blocks, mcp__plugin_tapistro_tapistro__add_journey_to_play, mcp__plugin_tapistro_tapistro__remove_journey_from_play, mcp__plugin_tapistro_tapistro__get_plays_for_journey, mcp__plugin_tapistro_tapistro__add_brand_context_content, mcp__plugin_tapistro_tapistro__update_brand_context_content, mcp__plugin_tapistro_tapistro__delete_brand_context_content, mcp__plugin_tapistro_tapistro__derive_brand_context, mcp__plugin_tapistro_tapistro__list_recipes, mcp__plugin_tapistro_tapistro__get_recipe, mcp__plugin_tapistro_tapistro__render_recipe
---

# Multi-threading

Input: $ARGUMENTS

The strategy: B2B purchases are decided by several people with different concerns, and a deal that depends on one contact dies when that person is busy, sceptical or leaves. Covering each role, with a message about that role's concern, makes the account resilient. In Tapistro a persona with one title group per role finds the committee, and the journey checks coverage per account.

Step types: `../build-journey/references/steps.md`. Persona fields: `../build-journey/references/persona.md`.

## 1. Define the committee with the user

From the brand context and recent wins, name the 3–4 roles that decide a purchase and what each cares about (for example: economic buyer — cost and risk; champion — their team's daily pain; technical evaluator — integration and security; end user — ease). For each role: titles, seniorities and departments, and how many people per account (`maxPersons` per title group).

Build one persona with a title group per role, so every person found carries the role it matched. Check it with `preview_persona` on 2–3 accounts the user knows well: every role should be found, with no wrong people.

## 2. Blueprint

```
SourceIngest / upstream tiering   target accounts (usually tier A from account-tiering)
  → PersonProfileCount            one count per role into account fields (cheap: counts, no full search)
  → Decision                      fully covered · partly covered · no coverage
      partly / no coverage → PersonSearch (committee persona) → PersonEmailValidation
  → Merge
  → Tap AI                        role-specific content agent: reads the person's title group and the account's signals
  → Destination                   sequencer (per-role sequences) and CRM (contacts linked to the account)
```

Why each piece:
- **Count before searching**, so accounts that already have the committee in the CRM do not pay for another search.
- **Role-specific copy**: the same account news means different things to each role; one agent that branches on the matched title group keeps the messages consistent with each other.
- **Same-day coordination**: send the roles into sequences that start together, so the account hears one coherent story rather than random touches.

## 3. Test, create, measure

- `preview_aiAgent` the content agent on two people with different roles at the same account; the messages should differ in concern, not just in name.
- Create via build-journey; start with a small `maxElements`.
- Measure coverage with `report_persons` per account (people per role) and outcomes with engagement signals (`email_replied_positive`, `meeting_booked`) per account, not per person: success is accounts with at least two engaged roles.
