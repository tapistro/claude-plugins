---
name: multichannel-sequence
description: Implement a multi-touch, multi-channel outreach cadence (email + LinkedIn, optionally ads) as a Tapistro journey — personalised copy per step from AI Agents, waits between touches, and branching on engagement so people who reply, book or bounce leave the sequence. Use when asked to build an outbound sequence or cadence, combine email and LinkedIn, personalise outreach at scale, or stop sequencing people who already responded.
argument-hint: "[audience or journey] [channels, e.g. 'email + LinkedIn']"
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
