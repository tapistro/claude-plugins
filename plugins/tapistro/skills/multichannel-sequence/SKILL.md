---
name: multichannel-sequence
description: Implement a multi-touch, multi-channel outreach cadence (email + LinkedIn, optionally ads) as a Tapistro journey — personalised copy per step from AI Agents, waits between touches, and branching on engagement so people who reply, book or bounce leave the sequence. Use when asked to build an outbound sequence or cadence, combine email and LinkedIn, personalise outreach at scale, or stop sequencing people who already responded.
argument-hint: "[audience or journey] [channels, e.g. 'email + LinkedIn']"
allowed-tools: mcp__plugin_tapistro_tapistro__list_journeys, mcp__plugin_tapistro_tapistro__get_journey, mcp__plugin_tapistro_tapistro__create_journey, mcp__plugin_tapistro_tapistro__update_journey, mcp__plugin_tapistro_tapistro__delete_journey, mcp__plugin_tapistro_tapistro__list_sources, mcp__plugin_tapistro_tapistro__get_source, mcp__plugin_tapistro_tapistro__create_source, mcp__plugin_tapistro_tapistro__update_source, mcp__plugin_tapistro_tapistro__delete_source, mcp__plugin_tapistro_tapistro__list_destinations, mcp__plugin_tapistro_tapistro__get_destination, mcp__plugin_tapistro_tapistro__create_destination, mcp__plugin_tapistro_tapistro__update_destination, mcp__plugin_tapistro_tapistro__delete_destination, mcp__plugin_tapistro_tapistro__list_connections, mcp__plugin_tapistro_tapistro__get_connection, mcp__plugin_tapistro_tapistro__list_personas, mcp__plugin_tapistro_tapistro__get_persona, mcp__plugin_tapistro_tapistro__create_persona, mcp__plugin_tapistro_tapistro__update_persona, mcp__plugin_tapistro_tapistro__delete_persona, mcp__plugin_tapistro_tapistro__list_aiAgents, mcp__plugin_tapistro_tapistro__get_aiAgent, mcp__plugin_tapistro_tapistro__create_aiAgent, mcp__plugin_tapistro_tapistro__update_aiAgent, mcp__plugin_tapistro_tapistro__delete_aiAgent, mcp__plugin_tapistro_tapistro__get_settings, mcp__plugin_tapistro_tapistro__update_settings, mcp__plugin_tapistro_tapistro__list_plays, mcp__plugin_tapistro_tapistro__get_play, mcp__plugin_tapistro_tapistro__create_play, mcp__plugin_tapistro_tapistro__update_play, mcp__plugin_tapistro_tapistro__delete_play, mcp__plugin_tapistro_tapistro__list_brandContexts, mcp__plugin_tapistro_tapistro__get_brandContext, mcp__plugin_tapistro_tapistro__create_brandContext, mcp__plugin_tapistro_tapistro__update_brandContext, mcp__plugin_tapistro_tapistro__delete_brandContext, mcp__plugin_tapistro_tapistro__report_accounts, mcp__plugin_tapistro_tapistro__report_account_by_id, mcp__plugin_tapistro_tapistro__report_persons, mcp__plugin_tapistro_tapistro__report_person_by_id, mcp__plugin_tapistro_tapistro__report_signals, mcp__plugin_tapistro_tapistro__report_activations, mcp__plugin_tapistro_tapistro__journey_funnel, mcp__plugin_tapistro_tapistro__journey_step_events, mcp__plugin_tapistro_tapistro__list_field_catalog, mcp__plugin_tapistro_tapistro__get_field_catalog_entry, mcp__plugin_tapistro_tapistro__get_preview, mcp__plugin_tapistro_tapistro__cancel_preview, mcp__plugin_tapistro_tapistro__preview_source, mcp__plugin_tapistro_tapistro__preview_persona, mcp__plugin_tapistro_tapistro__preview_aiAgent, mcp__plugin_tapistro_tapistro__list_versions, mcp__plugin_tapistro_tapistro__get_version, mcp__plugin_tapistro_tapistro__list_journey_runs, mcp__plugin_tapistro_tapistro__get_journey_run, mcp__plugin_tapistro_tapistro__run_journey, mcp__plugin_tapistro_tapistro__rerun_journey_from_step, mcp__plugin_tapistro_tapistro__cancel_journey_run, mcp__plugin_tapistro_tapistro__list_source_files, mcp__plugin_tapistro_tapistro__lookup, mcp__plugin_tapistro_tapistro__build_expression_ai, mcp__plugin_tapistro_tapistro__get_usage, mcp__plugin_tapistro_tapistro__get_summary_report, mcp__plugin_tapistro_tapistro__list_building_blocks, mcp__plugin_tapistro_tapistro__add_journey_to_play, mcp__plugin_tapistro_tapistro__remove_journey_from_play, mcp__plugin_tapistro_tapistro__get_plays_for_journey, mcp__plugin_tapistro_tapistro__add_brand_context_content, mcp__plugin_tapistro_tapistro__update_brand_context_content, mcp__plugin_tapistro_tapistro__delete_brand_context_content, mcp__plugin_tapistro_tapistro__derive_brand_context, mcp__plugin_tapistro_tapistro__list_recipes, mcp__plugin_tapistro_tapistro__get_recipe, mcp__plugin_tapistro_tapistro__render_recipe
---

# Multichannel sequence

Input: $ARGUMENTS

The strategy: one message rarely lands; several touches across channels, each adding something new, do — as long as anyone who responds stops getting automated touches. Tapistro can own the copy (AI Agents per step), the timing (Wait steps) and the exits (engagement signals flowing back from the sequencer), while the sequencer or LinkedIn tool does the sending.

Step types and engagement signal names: `../build-journey/references/steps.md`.

## 1. Decide who owns the cadence

- **The sequencer owns timing** (Outreach, SmartLead, Instantly, lemlist): Tapistro generates every step's copy once with a content agent using `emailSequenceScope: "all"` and pushes the person with the copy fields to the sequence. Simplest; use it when one channel dominates.
- **Tapistro owns timing**: the journey sends each touch to the channel's destination, waits, checks engagement, and continues. Use it to interleave channels or to route on replies.

Resolve sequence, campaign and LinkedIn-account ids with `lookup` (`outreach/sequences`, `smartlead/campaigns`, `instantly/campaigns`, `heyreach/campaigns`, `heyreach/linkedinaccounts`). Confirm with `list_sources` that the channel's engagement events come back as signals (e.g. an Outreach, SmartLead, lemlist or HeyReach feedback source); without them the journey cannot see replies.

## 2. Blueprint (Tapistro owns timing)

```
SourceIngest / upstream journey    people ready for outreach
  → PersonEmailValidation
  → Tap AI        content agent, emailSequenceScope "all": every step's copy in one pass
  → Destination   email step 1 + LinkedIn connect (no note)
  → Wait          2–3 days
  → Decision      replied / booked / bounced / unsubscribed  → exit branch
                  connection accepted                          → LinkedIn message
                  default                                      → next email
  → … repeat Wait → Decision → Destination per touch …
  → Merge → Destination (CRM: sequence finished, outcome recorded)
```

Exit conditions use the person's engagement signals since the sequence started: `email_replied`, `email_replied_positive`, `meeting_booked`, `email_bounced_hard`, `email_unsubscribed`, `linkedin_replied`; `linkedin_connection_accepted` unlocks the LinkedIn message. Send exits to the CRM so a person can pick up the conversation.

## 3. Copy per step

One `content` AI Agent writes the whole sequence (`emailSequenceScope: "all"`), or one agent per step (`{emailAt: n}`) when steps need different inputs. Each step must add something new: the first states the observation and the problem, later ones a different angle, something useful with no ask, then a short close. Feed it the account's signals, AI Agent findings and the brand context; have the write-outreach skill draft the first touch for a few real contacts so the user can approve the voice before it runs at scale.

Personalise by tier rather than evenly: deep research agents for tier-A accounts, role-and-industry copy for B, segment copy for C (tiers from account-tiering).

## 4. Test, create, measure

- `preview_aiAgent` the content agent on 3–5 real contacts; show every step's copy.
- Create via build-journey; first run with a small `maxElements` and a test sequence if the tool has one.
- Measure: `journey_funnel` (how many reach each touch, how many exit and why) and `report_signals` on engagement types by step. A touch that adds no replies is the one to rewrite or remove.
