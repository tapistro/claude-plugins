---
name: account-tiering
description: Implement ICP scoring and territory planning as a Tapistro journey — score every account for fit and timing with AI Agents, tier it A/B/C, give each tier its own treatment (deep personal outreach, scaled sequences, ads or nurture), and split tier-A accounts fairly across reps. Use when asked to score, tier, prioritise or segment accounts, build a lead-scoring model, plan territories, or route accounts to reps.
argument-hint: "[account source or segment] [reps or destinations]"
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
