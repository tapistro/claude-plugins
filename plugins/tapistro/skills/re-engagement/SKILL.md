---
name: re-engagement
description: Implement re-engagement plays as Tapistro journeys — revive closed-lost deals, stalled opportunities, churned customers or cold leads when something changes (a new signal, a new executive, the old champion moving to a new company), with a message that acknowledges the history. Use when asked to win back lost deals or churned customers, re-activate cold leads, follow champions who changed jobs, or do something with old CRM records.
argument-hint: "[segment, e.g. 'closed-lost last 12 months'] [CRM]"
---

# Re-engagement

Input: $ARGUMENTS

The strategy: people who already know you are the cheapest pipeline, but "just checking in" fails. Re-engagement works when something has changed since the last conversation — on their side (new leader, funding, new initiative, renewed intent) or yours (the missing feature shipped) — and the message names the history and the change. In Tapistro the CRM supplies the history and signals supply the change, so the journey only reaches out when there is a reason.

Step types and signal names: `../build-journey/references/steps.md`.

## 1. Find the segment and its history

- Source: a CRM list or report (`lookup` with `hubspot/lists` or `salesforce/reports`), or CRM object sources already in `list_sources`.
- The fields that carry the history: deal stage, closed-lost reason, close date, last activity, lifecycle stage. Find their paths with `list_field_catalog`; if the closed-lost reason is missing, the play is still possible but less precise, so tell the user.
- Agree the exclusions: lost to a competitor less than ~6 months ago, do-not-contact, open deals, current customers (for a win-back).

## 2. Blueprint

```
SourceIngest                      CRM list/report of lost, stalled or churned records
  → Filter                        exclusions (recent competitor loss, open deal, do-not-contact)
  → AccountProfileSignalEnrichment  what changed: new signals since the close date
  → Tap AI                        "what changed" agent: compares the lost reason with signals since,
                                    e.g. lost on price → new funding; lost on timing → executive_change;
                                    lost on missing feature → it shipped (from the brand context)
  → Decision                      reason to re-engage · nothing changed yet (default: stop)
  → PersonSearch                  the original contacts if still there; the new leader if one arrived
  → Tap AI                        content agent: acknowledges the history, names the change, low-pressure ask
  → Destination                   sequencer, and a CRM task for the owner of the original deal
```

A second, separate journey for **champion moves**: people in the CRM with `person_job_change` signals → Filter to those who were champions or users on won or late-stage deals → check the new company's fit (Tap AI, ICP) → content agent referencing their past success → Destination. A former champion at a good-fit company is often the warmest lead there is.

Why each piece:
- **No change, no message**: the Decision's default branch stops; the journey runs again later and picks the account up once a signal arrives.
- **Loop in the deal owner** with a CRM task, so a human who has the history can take over on a reply.

## 3. Test, create, measure

- `preview_aiAgent` the "what changed" agent on a few lost deals the user remembers; it should find the real change or say there is none.
- Create via build-journey, add to a play, start with a small `maxElements`.
- Measure: replies and meetings (`email_replied_positive`, `meeting_booked`) per lost reason, and `deal_stage_change` signals for reopened deals.
