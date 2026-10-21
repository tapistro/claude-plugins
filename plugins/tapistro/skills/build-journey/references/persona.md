# Personas

A persona says who to find at each account. `create_persona` body:

- `name`, optional `description`
- `titleGroups` (required, at least one): each `{name, titles?, keywords?, seniorities?, departments?, maxPersons?}`
  - `seniorities`: `c_suite`, `founder`, `owner`, `partner`, `vp`, `director`, `manager`, `senior`, `entry`
  - `departments`: `engineering`, `sales`, `marketing`, `finance`, `human_resources`, `operations`, `legal`, `customer_success`, `product`, `design`, `it`, `data_science`, `media`, `consulting`, `administrative`
  - `*FromAccountField` variants read the value from an account field instead of a fixed list
- `maxPersons` (required): cap per account across groups
- `includeLocations`, `exclusions` (titles to exclude), `validEmailOnly`, `allowPersonalEmailFallback`, `allowPersonsWithoutEmail`, `startDateFilterInDays`

Guidance:
- One title group per buying role (economic buyer, champion, user). Groups make results explainable.
- Titles match as substrings, so short acronyms over-match ("CRO" matches "Head of SEO & CRO"). Prefer full titles plus seniority, and put known false positives in `exclusions`.

Test: `preview_persona` with `{personaIdOrDefinition: <persona body or id>, domain: "<account domain>", searchSources?}` on 2–3 accounts the user knows. Poll `get_preview`. Show name, title and group per person, and adjust until the user agrees with the list.
