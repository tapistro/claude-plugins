---
name: ai-agent-builder
description: Design, test and tune a Tapistro AI Agent — an LLM or expression that classifies, scores, tiers, answers questions about, or writes content for each account or person. Previews it on real records before saving. Use when asked to classify, score, tier or qualify accounts or contacts, build an ICP-fit agent, research something per account, or fix an agent that tags records wrongly.
argument-hint: "[what the agent should decide, e.g. 'ICP fit: A/B/C']"
allowed-tools: mcp__plugin_tapistro_tapistro__list_journeys, mcp__plugin_tapistro_tapistro__get_journey, mcp__plugin_tapistro_tapistro__create_journey, mcp__plugin_tapistro_tapistro__update_journey, mcp__plugin_tapistro_tapistro__delete_journey, mcp__plugin_tapistro_tapistro__list_sources, mcp__plugin_tapistro_tapistro__get_source, mcp__plugin_tapistro_tapistro__create_source, mcp__plugin_tapistro_tapistro__update_source, mcp__plugin_tapistro_tapistro__delete_source, mcp__plugin_tapistro_tapistro__list_destinations, mcp__plugin_tapistro_tapistro__get_destination, mcp__plugin_tapistro_tapistro__create_destination, mcp__plugin_tapistro_tapistro__update_destination, mcp__plugin_tapistro_tapistro__delete_destination, mcp__plugin_tapistro_tapistro__list_connections, mcp__plugin_tapistro_tapistro__get_connection, mcp__plugin_tapistro_tapistro__list_personas, mcp__plugin_tapistro_tapistro__get_persona, mcp__plugin_tapistro_tapistro__create_persona, mcp__plugin_tapistro_tapistro__update_persona, mcp__plugin_tapistro_tapistro__delete_persona, mcp__plugin_tapistro_tapistro__list_aiAgents, mcp__plugin_tapistro_tapistro__get_aiAgent, mcp__plugin_tapistro_tapistro__create_aiAgent, mcp__plugin_tapistro_tapistro__update_aiAgent, mcp__plugin_tapistro_tapistro__delete_aiAgent, mcp__plugin_tapistro_tapistro__get_settings, mcp__plugin_tapistro_tapistro__update_settings, mcp__plugin_tapistro_tapistro__list_plays, mcp__plugin_tapistro_tapistro__get_play, mcp__plugin_tapistro_tapistro__create_play, mcp__plugin_tapistro_tapistro__update_play, mcp__plugin_tapistro_tapistro__delete_play, mcp__plugin_tapistro_tapistro__list_brandContexts, mcp__plugin_tapistro_tapistro__get_brandContext, mcp__plugin_tapistro_tapistro__create_brandContext, mcp__plugin_tapistro_tapistro__update_brandContext, mcp__plugin_tapistro_tapistro__delete_brandContext, mcp__plugin_tapistro_tapistro__report_accounts, mcp__plugin_tapistro_tapistro__report_account_by_id, mcp__plugin_tapistro_tapistro__report_persons, mcp__plugin_tapistro_tapistro__report_person_by_id, mcp__plugin_tapistro_tapistro__report_signals, mcp__plugin_tapistro_tapistro__report_activations, mcp__plugin_tapistro_tapistro__journey_funnel, mcp__plugin_tapistro_tapistro__journey_step_events, mcp__plugin_tapistro_tapistro__list_field_catalog, mcp__plugin_tapistro_tapistro__get_field_catalog_entry, mcp__plugin_tapistro_tapistro__get_preview, mcp__plugin_tapistro_tapistro__cancel_preview, mcp__plugin_tapistro_tapistro__preview_source, mcp__plugin_tapistro_tapistro__preview_persona, mcp__plugin_tapistro_tapistro__preview_aiAgent, mcp__plugin_tapistro_tapistro__list_versions, mcp__plugin_tapistro_tapistro__get_version, mcp__plugin_tapistro_tapistro__list_journey_runs, mcp__plugin_tapistro_tapistro__get_journey_run, mcp__plugin_tapistro_tapistro__run_journey, mcp__plugin_tapistro_tapistro__rerun_journey_from_step, mcp__plugin_tapistro_tapistro__cancel_journey_run, mcp__plugin_tapistro_tapistro__list_source_files, mcp__plugin_tapistro_tapistro__lookup, mcp__plugin_tapistro_tapistro__build_expression_ai, mcp__plugin_tapistro_tapistro__get_usage, mcp__plugin_tapistro_tapistro__get_summary_report, mcp__plugin_tapistro_tapistro__list_building_blocks, mcp__plugin_tapistro_tapistro__add_journey_to_play, mcp__plugin_tapistro_tapistro__remove_journey_from_play, mcp__plugin_tapistro_tapistro__get_plays_for_journey, mcp__plugin_tapistro_tapistro__add_brand_context_content, mcp__plugin_tapistro_tapistro__update_brand_context_content, mcp__plugin_tapistro_tapistro__delete_brand_context_content, mcp__plugin_tapistro_tapistro__derive_brand_context, mcp__plugin_tapistro_tapistro__list_recipes, mcp__plugin_tapistro_tapistro__get_recipe, mcp__plugin_tapistro_tapistro__render_recipe
---

# AI Agent builder

Goal: $ARGUMENTS

The API calls AI Agents computed attributes; their results appear on records and in journey payloads as `computed:<id>`. Talk to the user about "AI Agents".

## Pick the type

| `evaluationConfigType` | Use for | `evaluationConfig` |
|---|---|---|
| `classification` | One label from a fixed set (tier, fit, persona match) | `{possibleValues: [{name, description}], includeCitationAndReason}` |
| `expression` | A deterministic rule over existing fields; no LLM cost | `{expression}`; draft it with `build_expression_ai` |
| `questions` | Several named answers per record; always returns citations and reasoning | `{questions: [{question, name, answerType}]}` |
| `content` | Generated text (summary, opener) | `{instruction}` |
| `assignment` | Owner or bucket assignment | `{}` |

Prefer `expression` when the rule needs only existing fields: it is exact, free and instant. Use an LLM type only when the decision needs judgement or web research.

Other fields: `name`, `description` (this is the instruction the LLM follows — write it like a brief to an analyst), `targetObject` (`account`, `person`, `signal`, `activation`), `valueType` (`string`, `number`, `boolean`, `infer`), `inputAttributes` (field paths the agent reads; find them with `list_field_catalog`), optional `inputUrlAttributes` (fields holding URLs to read).

## Citations

For `classification`, set `includeCitationAndReason: true` unless the user says not to. The result then carries the label with the reasoning and sources, so users can see why a record got its label. The stored value becomes an object (`category`, `reasoning`, `__citations`) instead of a bare string. Before turning it on for an existing agent, `list_journeys` + `get_journey` to find steps whose filters reference `computed:<id>`, and show the user which ones need updating.

## Write good labels

- Each `possibleValues` entry gets a description that states the decision rule, with the evidence that puts a record there. Mutually exclusive, collectively exhaustive; include an "unknown / not enough data" value.
- Ground the `description` in the brand context (`get_brandContext` → ICP, disqualifiers) rather than generic ICP language.
- Name only the input fields the decision needs. Extra fields add cost and noise.

## Test before saving

1. Pick 3–5 records the user can judge: clear fits, clear misses, one borderline (`report_accounts` / `report_persons`).
2. `preview_aiAgent` with the full body plus `objectId` for each; poll `get_preview` until Success.
3. Show a table: record → expected → agent's answer → reasoning. Revise the description or labels where it is wrong and preview again.
4. Only when the user is happy: `create_aiAgent` (or `update_aiAgent` with the full object — updates are full replacements).

Never create or update without the user's explicit go-ahead.
