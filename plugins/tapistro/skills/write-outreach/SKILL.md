---
name: write-outreach
description: Draft a first-touch cold email or LinkedIn message to a specific contact, grounded in Tapistro data (recent signals, AI Agent verdicts, the account's profile) and the company's brand context and voice. Short, specific, never invents facts. Use when asked to write an email, opener or LinkedIn note to someone at an account, or draft outreach for a list of accounts. Not for sequences, follow-ups or replies to warm threads.
argument-hint: "[contact email or name @ domain] [channel: email | linkedin]"
allowed-tools: mcp__plugin_tapistro_tapistro__list_journeys, mcp__plugin_tapistro_tapistro__get_journey, mcp__plugin_tapistro_tapistro__create_journey, mcp__plugin_tapistro_tapistro__update_journey, mcp__plugin_tapistro_tapistro__delete_journey, mcp__plugin_tapistro_tapistro__list_sources, mcp__plugin_tapistro_tapistro__get_source, mcp__plugin_tapistro_tapistro__create_source, mcp__plugin_tapistro_tapistro__update_source, mcp__plugin_tapistro_tapistro__delete_source, mcp__plugin_tapistro_tapistro__list_destinations, mcp__plugin_tapistro_tapistro__get_destination, mcp__plugin_tapistro_tapistro__create_destination, mcp__plugin_tapistro_tapistro__update_destination, mcp__plugin_tapistro_tapistro__delete_destination, mcp__plugin_tapistro_tapistro__list_connections, mcp__plugin_tapistro_tapistro__get_connection, mcp__plugin_tapistro_tapistro__list_personas, mcp__plugin_tapistro_tapistro__get_persona, mcp__plugin_tapistro_tapistro__create_persona, mcp__plugin_tapistro_tapistro__update_persona, mcp__plugin_tapistro_tapistro__delete_persona, mcp__plugin_tapistro_tapistro__list_aiAgents, mcp__plugin_tapistro_tapistro__get_aiAgent, mcp__plugin_tapistro_tapistro__create_aiAgent, mcp__plugin_tapistro_tapistro__update_aiAgent, mcp__plugin_tapistro_tapistro__delete_aiAgent, mcp__plugin_tapistro_tapistro__get_settings, mcp__plugin_tapistro_tapistro__update_settings, mcp__plugin_tapistro_tapistro__list_plays, mcp__plugin_tapistro_tapistro__get_play, mcp__plugin_tapistro_tapistro__create_play, mcp__plugin_tapistro_tapistro__update_play, mcp__plugin_tapistro_tapistro__delete_play, mcp__plugin_tapistro_tapistro__list_brandContexts, mcp__plugin_tapistro_tapistro__get_brandContext, mcp__plugin_tapistro_tapistro__create_brandContext, mcp__plugin_tapistro_tapistro__update_brandContext, mcp__plugin_tapistro_tapistro__delete_brandContext, mcp__plugin_tapistro_tapistro__report_accounts, mcp__plugin_tapistro_tapistro__report_account_by_id, mcp__plugin_tapistro_tapistro__report_persons, mcp__plugin_tapistro_tapistro__report_person_by_id, mcp__plugin_tapistro_tapistro__report_signals, mcp__plugin_tapistro_tapistro__report_activations, mcp__plugin_tapistro_tapistro__journey_funnel, mcp__plugin_tapistro_tapistro__journey_step_events, mcp__plugin_tapistro_tapistro__list_field_catalog, mcp__plugin_tapistro_tapistro__get_field_catalog_entry, mcp__plugin_tapistro_tapistro__get_preview, mcp__plugin_tapistro_tapistro__cancel_preview, mcp__plugin_tapistro_tapistro__preview_source, mcp__plugin_tapistro_tapistro__preview_persona, mcp__plugin_tapistro_tapistro__preview_aiAgent, mcp__plugin_tapistro_tapistro__list_versions, mcp__plugin_tapistro_tapistro__get_version, mcp__plugin_tapistro_tapistro__list_journey_runs, mcp__plugin_tapistro_tapistro__get_journey_run, mcp__plugin_tapistro_tapistro__run_journey, mcp__plugin_tapistro_tapistro__rerun_journey_from_step, mcp__plugin_tapistro_tapistro__cancel_journey_run, mcp__plugin_tapistro_tapistro__list_source_files, mcp__plugin_tapistro_tapistro__lookup, mcp__plugin_tapistro_tapistro__build_expression_ai, mcp__plugin_tapistro_tapistro__get_usage, mcp__plugin_tapistro_tapistro__get_summary_report, mcp__plugin_tapistro_tapistro__list_building_blocks, mcp__plugin_tapistro_tapistro__add_journey_to_play, mcp__plugin_tapistro_tapistro__remove_journey_from_play, mcp__plugin_tapistro_tapistro__get_plays_for_journey, mcp__plugin_tapistro_tapistro__add_brand_context_content, mcp__plugin_tapistro_tapistro__update_brand_context_content, mcp__plugin_tapistro_tapistro__delete_brand_context_content, mcp__plugin_tapistro_tapistro__derive_brand_context, mcp__plugin_tapistro_tapistro__list_recipes, mcp__plugin_tapistro_tapistro__get_recipe, mcp__plugin_tapistro_tapistro__render_recipe
---

# Write outreach

Target: $ARGUMENTS

## Gather

1. Contact and account: `report_persons` (by `emailId`, or `accountId` + name) and `report_account_by_id`, including the `computed:<id>` fields from `list_aiAgents`.
2. Recent signals for the account (`report_signals`, last 30 days).
3. Brand context (`list_brandContexts` → `get_brandContext`): what we sell, the problem it solves for this contact's role, proof points, and voice.

## The observation comes from data

The opening line is one specific, current fact about them: a signal (visited the pricing page, researching a topic), a hire, a launch, an AI Agent finding with its citation. If the data has nothing specific, say so and ask the user for one. Never invent or embellish facts about a real company or person, and never reveal tracking ("I saw you on our pricing page"); describe the underlying need instead.

## Write

Lead with that fact, connect it to a problem this person's role owns (from the brand context), and end with an easy question. Keep it short enough to read on a phone, in the brand's voice, about them rather than us. If the message could be sent unchanged to someone else, the fact is not specific enough.

Return the subject and body, then the angle in one sentence and the data points used with their dates. For several contacts, one draft each and a table of angles.

## From one draft to a journey

Hand-drafting suits a few accounts. When the user approves the voice, offer to turn it into a content AI Agent (ai-agent-builder) whose instruction encodes the angle and the approved draft as the example, so a journey writes the same quality for every contact; multichannel-sequence and signal-triggered-outreach show where it fits.

Drafts only. Sending happens through the user's destination or sequencer; ask before pushing anything there.
