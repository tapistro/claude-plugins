---
name: build-journey
description: Build or extend a Tapistro journey end to end — source → AI Agents and filters → persona → destination — previewing each piece on real records before anything is saved, then attaching it to a play. Starts from a recipe template when one fits. Use when asked to set up, build or automate a journey, workflow, play or GTM motion, e.g. "push tier-1 accounts to Outreach" or "find contacts at intent accounts and sync to HubSpot".
argument-hint: "[goal, e.g. 'tier accounts by intent and push to HubSpot']"
allowed-tools: mcp__plugin_tapistro_tapistro__list_journeys, mcp__plugin_tapistro_tapistro__get_journey, mcp__plugin_tapistro_tapistro__create_journey, mcp__plugin_tapistro_tapistro__update_journey, mcp__plugin_tapistro_tapistro__delete_journey, mcp__plugin_tapistro_tapistro__list_sources, mcp__plugin_tapistro_tapistro__get_source, mcp__plugin_tapistro_tapistro__create_source, mcp__plugin_tapistro_tapistro__update_source, mcp__plugin_tapistro_tapistro__delete_source, mcp__plugin_tapistro_tapistro__list_destinations, mcp__plugin_tapistro_tapistro__get_destination, mcp__plugin_tapistro_tapistro__create_destination, mcp__plugin_tapistro_tapistro__update_destination, mcp__plugin_tapistro_tapistro__delete_destination, mcp__plugin_tapistro_tapistro__list_connections, mcp__plugin_tapistro_tapistro__get_connection, mcp__plugin_tapistro_tapistro__list_personas, mcp__plugin_tapistro_tapistro__get_persona, mcp__plugin_tapistro_tapistro__create_persona, mcp__plugin_tapistro_tapistro__update_persona, mcp__plugin_tapistro_tapistro__delete_persona, mcp__plugin_tapistro_tapistro__list_aiAgents, mcp__plugin_tapistro_tapistro__get_aiAgent, mcp__plugin_tapistro_tapistro__create_aiAgent, mcp__plugin_tapistro_tapistro__update_aiAgent, mcp__plugin_tapistro_tapistro__delete_aiAgent, mcp__plugin_tapistro_tapistro__get_settings, mcp__plugin_tapistro_tapistro__update_settings, mcp__plugin_tapistro_tapistro__list_plays, mcp__plugin_tapistro_tapistro__get_play, mcp__plugin_tapistro_tapistro__create_play, mcp__plugin_tapistro_tapistro__update_play, mcp__plugin_tapistro_tapistro__delete_play, mcp__plugin_tapistro_tapistro__list_brandContexts, mcp__plugin_tapistro_tapistro__get_brandContext, mcp__plugin_tapistro_tapistro__create_brandContext, mcp__plugin_tapistro_tapistro__update_brandContext, mcp__plugin_tapistro_tapistro__delete_brandContext, mcp__plugin_tapistro_tapistro__report_accounts, mcp__plugin_tapistro_tapistro__report_account_by_id, mcp__plugin_tapistro_tapistro__report_persons, mcp__plugin_tapistro_tapistro__report_person_by_id, mcp__plugin_tapistro_tapistro__report_signals, mcp__plugin_tapistro_tapistro__report_activations, mcp__plugin_tapistro_tapistro__journey_funnel, mcp__plugin_tapistro_tapistro__journey_step_events, mcp__plugin_tapistro_tapistro__list_field_catalog, mcp__plugin_tapistro_tapistro__get_field_catalog_entry, mcp__plugin_tapistro_tapistro__get_preview, mcp__plugin_tapistro_tapistro__cancel_preview, mcp__plugin_tapistro_tapistro__preview_source, mcp__plugin_tapistro_tapistro__preview_persona, mcp__plugin_tapistro_tapistro__preview_aiAgent, mcp__plugin_tapistro_tapistro__list_versions, mcp__plugin_tapistro_tapistro__get_version, mcp__plugin_tapistro_tapistro__list_journey_runs, mcp__plugin_tapistro_tapistro__get_journey_run, mcp__plugin_tapistro_tapistro__run_journey, mcp__plugin_tapistro_tapistro__rerun_journey_from_step, mcp__plugin_tapistro_tapistro__cancel_journey_run, mcp__plugin_tapistro_tapistro__list_source_files, mcp__plugin_tapistro_tapistro__lookup, mcp__plugin_tapistro_tapistro__build_expression_ai, mcp__plugin_tapistro_tapistro__get_usage, mcp__plugin_tapistro_tapistro__get_summary_report, mcp__plugin_tapistro_tapistro__list_building_blocks, mcp__plugin_tapistro_tapistro__add_journey_to_play, mcp__plugin_tapistro_tapistro__remove_journey_from_play, mcp__plugin_tapistro_tapistro__get_plays_for_journey, mcp__plugin_tapistro_tapistro__add_brand_context_content, mcp__plugin_tapistro_tapistro__update_brand_context_content, mcp__plugin_tapistro_tapistro__delete_brand_context_content, mcp__plugin_tapistro_tapistro__derive_brand_context, mcp__plugin_tapistro_tapistro__list_recipes, mcp__plugin_tapistro_tapistro__get_recipe, mcp__plugin_tapistro_tapistro__render_recipe
---

# Build a journey

Goal: $ARGUMENTS

## 1. Shape it with the user

Restate the goal as a pipeline and confirm it: which accounts come in (source), how they are qualified (AI Agents, filters), who is contacted (persona), where they go (destination). Ask only what you cannot resolve yourself.

## 2. Reuse before creating

- `list_recipes` → `get_recipe` for the closest template. A recipe fixes step order and wiring that is easy to get wrong.
- `list_sources`, `list_aiAgents`, `list_personas`, `list_destinations`, `list_connections`: reuse existing entities when they fit.
- `lookup` resolves integration ids (`hubspot/lists`, `outreach/sequences`, `linkedin/campaigns` with `adAccountId`, …) instead of asking the user for them.
- `list_building_blocks` (`step`, `source`, `destination`) for kinds to choose from; skip deprecated ones.

## 3. Build and preview each new piece

Preview with real data before creating anything; show the user what each piece produces.

- Source: see `references/source.md`.
- AI Agent: follow the ai-agent-builder skill.
- Persona: see `references/persona.md`.
- Filter expressions: draft with `build_expression_ai`; check field paths with `list_field_catalog`.

Every preview tool starts a job: poll `get_preview` every few seconds until Success or Failed.
## 4. Assemble

- From a recipe: `render_recipe` with the variable answers. It creates nothing; it returns the payload.
- From scratch: in `create_journey`, new steps and edges use unique integer ids (0, 1, 2…) that edge `from`/`to` reference. Never invent string ULIDs; the backend assigns them.

Show the user a summary — steps in order, each with its config in one line, and the destination — and get explicit approval.

## 5. Create, attach, verify

1. Create entities in dependency order: source, AI Agents, persona, destination, then the journey.
2. Offer to add it to a play (`list_plays`, `add_journey_to_play`).
3. `get_journey` to confirm the graph. Ask before `run_journey`: a run pulls fresh records and spends credits.

Never create, update, delete or run without the user's explicit go-ahead. `update_*` tools replace the whole object: get it, change it, send it all back.
