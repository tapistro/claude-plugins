# Journey step reference

Each step is `{id, name, config}`; `config.type` picks the kind. Edges are `{id, from, to}`; an edge leaving a Decision step also carries `conditionId`, naming the branch it follows. Before writing a config, `get_journey` an existing journey that uses the same step type and copy its shape: field names below come from the backend, but expression syntax and nested objects are easiest to get right by example. `list_building_blocks` with `kind: "step"` lists every type with its description and whether it is deprecated.

| Type | UI name | What it does | Key config |
|---|---|---|---|
| `SourceIngest` | Source Ingest | Pulls accounts, people or signals from a source | `sourceId` |
| `SourceDiscovery` | Source Discovery | Turns signals into account/person profiles | `workEmailValidation?` |
| `AccountProfileEnrichment` / `PersonProfileEnrichment` | Data Enrichment | Firmographic or contact data from providers | `enrichmentParams`, `refreshDurationInDays?`, `requiredFields?` |
| `AccountProfileSignalEnrichment` | Account Signal Enrichment | Adds signals from a source (intent, ads, jobs, network) to accounts | `sourceId`, `maxSignalsPerAccount?` |
| `ComputedAttributeEvaluation` | Tap AI | Runs AI Agents on each record; results become `computed:<id>` fields | `computedAttributeIds`, `emailSequenceScope?` (`"all"` or `{emailAt: n}`), `refreshDurationInDays?` |
| `Filter` | Data Filter | Keeps records matching an expression | `expression`, `targetOn?` (`account` / `input`) |
| `Decision` | Conditional Branch | Routes records down the first matching branch | `conditions: [{id, expression}]`, `defaultConditionId?` |
| `TopN` | Top N | Keeps the best N by sort order (waits for the whole batch) | `n`, `sortBy: [{attributeName, direction}]` |
| `Distribution` | Distribute Across Team | Splits records into weighted buckets (e.g. reps) | `buckets: [{id, share, name?}]`, `expression` |
| `AssignField` | Assign Field | Writes values to assignment AI Agents via expressions | `assignments: [{computedAttributeId, evaluationConfig}]` |
| `PersonSearch` | Person Search | Finds people at each account using a persona. Still runs, but `list_building_blocks` flags it deprecated: if the tenant's journeys find people another way, copy that | `personaId`, `searchSources?`, `workEmailValidation?` |
| `PersonProfileCount` | Person Count | Counts matching people per account into an account field | `accountProfileFieldName`, title/seniority/department filters |
| `PersonEmailValidation` | Person Email Validation | Verifies work emails before sending | `workEmailValidation` |
| `Wait` | Wait | Holds records before the next step | `waitInMinutes` |
| `Merge` | Merge | Joins several branches back into one | — |
| `Destination` | Destination | Sends records to a CRM, ad platform or sequencer | `destinationId`, `targetOn?` |

Expressions reference fields by path: catalog fields (`list_field_catalog`), AI Agent results (`computed:<id>`), and related records (`SIGNALS.<field>`, `ACTIVATIONS.<field>`). Draft them with `build_expression_ai`.

Signals carry a `normalizedSignalType`. The ones strategies use most:
- Company events: `funding_round`, `new_investment`, `executive_change`, `new_hire`, `job_posted`, `hiring_surge`, `headcount_increase`, `new_office`, `new_product_launch`, `merger_or_acquisition`, `technology_adopted`; negative: `headcount_decrease`, `cost_cutting`, `office_closure`, `legal_issue`, `security_breach`
- Intent: `high_intent_page_visit`, `repeat_visit`, `page_visit`, `topic_intent`, `intent_observed`, `demo_requested`, `form_submitted`, `content_downloaded`, `webinar_attended`, `trial_started`
- People: `person_job_change`
- Engagement with our outreach: `email_opened`, `email_clicked`, `email_replied`, `email_replied_positive`, `email_replied_negative`, `email_bounced_hard`, `email_unsubscribed`, `linkedin_connection_accepted`, `linkedin_replied`, `meeting_booked`
- CRM: `deal_stage_change`, `lifecycle_stage_change`, `crm_deal_record`

Only signal types the tenant's sources produce will exist: check with `report_signals` before designing around one.
