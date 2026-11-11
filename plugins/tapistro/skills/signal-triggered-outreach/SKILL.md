---
name: signal-triggered-outreach
description: Implement trigger-based selling as a Tapistro journey — when an account shows a buying trigger (funding, new executive, hiring for the buyer's role, tech adoption, high-intent visits, topic intent), qualify it, find the right people, write an angle specific to that trigger, and push it to a sequencer or CRM within the trigger's window. Use when asked to act on signals or trigger events automatically, "reach out when X happens", or set up signal-based plays.
argument-hint: "[trigger(s), e.g. 'funding and new VP Sales'] [destination]"
---

# Signal-triggered outreach

Input: $ARGUMENTS

The strategy: a trigger is a reason to talk to a specific person now. It decays quickly, it implies a problem for one role, and the message must be about that consequence, not the event. In Tapistro the whole loop is one journey, so it runs every time new signals arrive instead of depending on someone reading a dashboard.

Step types and signal names: `../build-journey/references/steps.md`.

## 1. Choose the triggers from real data

`report_signals` over the last 90 days, counted by `normalizedSignalType`. Agree with the user which types are triggers for their product (the brand context says which problems it solves) and, per type: the role that owns the consequence, and how many days the trigger stays fresh. Treat `headcount_decrease`, `cost_cutting`, `security_breach` and a recent competitor `technology_adopted` as suppressions, not triggers, unless the product addresses exactly that.

## 2. Blueprint

```
SourceIngest / AccountProfileSignalEnrichment   signals or accounts with signals
  → Filter            trigger type in the agreed set AND signal within its freshness window
  → Tap AI            fit agent (ICP) + trigger-angle agent
  → Filter            fit is good enough; drop suppressed accounts
  → Decision          one branch per trigger family (funding · leadership · hiring · intent)
      each branch → PersonSearch (persona for the role that owns that trigger)
  → Merge
  → PersonEmailValidation
  → Tap AI            outreach content agent, fed the trigger and the angle
  → Destination       sequencer or CRM (resolve campaign/list ids with lookup)
```

Why each piece:
- **Freshness in the first filter**, so stale triggers never spend AI credits.
- **Fit before people search**: searching people costs credits per account; filter first.
- **Branch by trigger family**, because each family targets a different buyer role (a funding round matters to the team that must deliver on it; a new VP is the person to reach; a job post names the team that is hiring).
- **Trigger-angle agent** (`content`, or `questions` with `whatChanged`, `whoOwnsIt`, `angle`): describe the event's consequence for that role, cite the signal, and never reveal tracking of web visits.
- **Content agent last**, so it writes from the angle, the person and the brand context. Use `emailSequenceScope` when the destination is a multi-step sequence.

Reuse an existing fit agent and personas where they exist (`list_aiAgents`, `list_personas`).

## 3. Test on real triggers

- `preview_aiAgent` the angle and content agents on 3–5 accounts that recently had each trigger; show trigger → angle → first line.
- `preview_persona` per branch on one of those accounts.
- Agree the filters' expressions with the user (`build_expression_ai`).

## 4. Create and run

Follow build-journey for creation order, the integer-id rule and approval. Add the journey to a play named after the strategy. Before the first `run_journey`, set `maxElements` low (e.g. 25) and review the destination records with the user.

## 5. Measure

After a week: `journey_funnel` (where triggers drop out), and `report_signals` for `email_replied_positive` and `meeting_booked` on activated contacts, broken down by trigger branch. Keep the triggers that convert; tighten or drop the rest by updating the first filter.
