---
name: build-journey
description: Build a new Tapistro journey, preferably from a recipe template — pick a recipe, collect its variables, render it, review the payload with the user, then create the entities. Use when asked to set up, create, or build a journey, play, or GTM workflow.
argument-hint: "[goal, e.g. 'tier accounts by intent and push to HubSpot']"
allowed-tools: mcp__plugin_tapistro_tapistro__list_recipes, mcp__plugin_tapistro_tapistro__get_recipe, mcp__plugin_tapistro_tapistro__render_recipe, mcp__plugin_tapistro_tapistro__list_journey_step_operation_types, mcp__plugin_tapistro_tapistro__list_activation_destination_types, mcp__plugin_tapistro_tapistro__list_sources, mcp__plugin_tapistro_tapistro__list_destinations, mcp__plugin_tapistro_tapistro__list_connections, mcp__plugin_tapistro_tapistro__lookup, mcp__plugin_tapistro_tapistro__build_expression_ai, mcp__plugin_tapistro_tapistro__get_journey
---

# Build a journey

Goal: $ARGUMENTS

1. `list_recipes` and pick the closest match; `get_recipe` to read its variables. If none fit, build from scratch (step 5).
2. Collect variable values. Resolve ids instead of asking for them: `list_sources`, `list_destinations`, `list_connections`, and `lookup` for integration records (e.g. `resource: "hubspot/lists"`, `"linkedin/campaigns"` with `adAccountId`). Use `build_expression_ai` to turn plain-language filters into expressions.
3. `render_recipe` — it creates nothing. Show the user the rendered payload as a short summary (entities, steps, destinations) and get explicit approval.
4. Only after approval, create the entities in the order the payload lists them with the matching `create_*` tools.
5. From scratch: check available step kinds with `list_journey_step_operation_types` and destinations with `list_activation_destination_types`. In `create_journey`, new steps and edges use unique INTEGER ids (0, 1, 2…) referenced by edge `from`/`to`; never invent string ULIDs.
6. Confirm the result with `get_journey` and report its id and step list.

Never create, update, or delete without the user's explicit go-ahead. `update_*` tools are full replacements: get, modify, send back the whole object.
