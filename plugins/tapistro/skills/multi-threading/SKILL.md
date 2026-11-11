---
name: multi-threading
description: Implement buying-committee coverage (multi-threading) as a Tapistro journey — for each target account find people in every buying role (economic buyer, champion, technical evaluator, end user), check which roles are covered, fill the gaps, and send each role a message about its own concern. Use when asked to reach several people per account, map or cover the buying committee, avoid single-threaded deals, or find who else to contact at an account.
argument-hint: "[accounts or journey] [roles]"
---

# Multi-threading

Input: $ARGUMENTS

The strategy: B2B purchases are decided by several people with different concerns, and a deal that depends on one contact dies when that person is busy, sceptical or leaves. Covering each role, with a message about that role's concern, makes the account resilient. In Tapistro a persona with one title group per role finds the committee, and the journey checks coverage per account.

Step types: `../build-journey/references/steps.md`. Persona fields: `../build-journey/references/persona.md`.

## 1. Define the committee with the user

From the brand context and recent wins, name the 3–4 roles that decide a purchase and what each cares about (for example: economic buyer — cost and risk; champion — their team's daily pain; technical evaluator — integration and security; end user — ease). For each role: titles, seniorities and departments, and how many people per account (`maxPersons` per title group).

Build one persona with a title group per role, so every person found carries the role it matched. Check it with `preview_persona` on 2–3 accounts the user knows well: every role should be found, with no wrong people.

## 2. Blueprint

```
SourceIngest / upstream tiering   target accounts (usually tier A from account-tiering)
  → PersonProfileCount            one count per role into account fields (cheap: counts, no full search)
  → Decision                      fully covered · partly covered · no coverage
      partly / no coverage → PersonSearch (committee persona) → PersonEmailValidation
  → Merge
  → Tap AI                        role-specific content agent: reads the person's title group and the account's signals
  → Destination                   sequencer (per-role sequences) and CRM (contacts linked to the account)
```

Why each piece:
- **Count before searching**, so accounts that already have the committee in the CRM do not pay for another search.
- **Role-specific copy**: the same account news means different things to each role; one agent that branches on the matched title group keeps the messages consistent with each other.
- **Same-day coordination**: send the roles into sequences that start together, so the account hears one coherent story rather than random touches.

## 3. Test, create, measure

- `preview_aiAgent` the content agent on two people with different roles at the same account; the messages should differ in concern, not just in name.
- Create via build-journey; start with a small `maxElements`.
- Measure coverage with `report_persons` per account (people per role) and outcomes with engagement signals (`email_replied_positive`, `meeting_booked`) per account, not per person: success is accounts with at least two engaged roles.
