---
name: build-journey
description: Build or extend a Tapistro journey end to end — source → AI Agents and filters → persona → destination — previewing each piece on real records before anything is saved, then attaching it to a play. Starts from a recipe template when one fits. Use when asked to set up, build or automate a journey, workflow, play or GTM motion, e.g. "push tier-1 accounts to Outreach" or "find contacts at intent accounts and sync to HubSpot".
argument-hint: "[goal, e.g. 'tier accounts by intent and push to HubSpot']"
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
