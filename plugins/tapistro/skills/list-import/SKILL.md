---
name: list-import
description: Research accounts or people on the web (e.g. startups in the news — funding, launches, executive hires) and load the list into Tapistro as a CSV source, with the column mapping set up so later runs load without review. Use for "find startups that raised this week and add them to Tapistro", "build a list of founders in the news", or a scheduled task that refreshes such a list.
argument-hint: "[what to find, e.g. 'seed-stage AI startups that raised this week']"
---

# List import

Find: $ARGUMENTS

1. **Research.** Search the web for records that match. Keep only what a source states; record its URL. Skip a record you cannot tie to a company domain (accounts) or a name and company (people).
2. **Shape the rows.** One row per record, as column -> value. Use the same column names on every run into a source — saved mappings match on them:
   - Accounts: `name`, `domain`, `linkedInUrl`, `description`, `newsHeadline`, `newsUrl`, `newsDate`.
   - People: `fullName`, `title`, `email`, `linkedInUrl`, `companyName`, `companyDomain`, `newsHeadline`, `newsUrl`, `newsDate`.
   Leave a value out rather than guessing it. Show the user the rows (or the first 20 and a count) before importing.
3. **Import.** `import_records` with `source: {newName}` the first time, or `source: {id}` to add to an existing one (`list_sources` finds it). At most 500 rows per call; split larger lists.
4. **Mapping, first run only.** Poll `get_source_file` until the status is `SuggestionAdded`. Show the suggested mapping as a table (column -> Tapistro field), call out unmapped columns, and after the user confirms save it with `set_source_mapping`.
5. **Later runs.** With the same columns the file is approved without review: check `get_source_file` reaches `Approved` or `Done` and report the count. If it stops at `SuggestionAdded`, the columns changed — tell the user; do not change the mapping yourself.

Report: source name and id, rows imported, file status, and the sources used for the research.

## Running on a schedule

Run once interactively so the source and its mapping exist, then schedule the same request naming that source (e.g. "every Monday, find … and add them to the Startup news source"). Scheduled runs need the Tapistro write tools allowed without a prompt.

Never import without the user's go-ahead in an interactive run.
