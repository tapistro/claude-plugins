# Sources

A source decides which accounts or people enter the journey: a CRM list, a CSV upload, a web-visit or intent provider, or an account search.

- `list_building_blocks` with `kind: "source"` for the available kinds; `list_connections` for the connected integrations they need.
- Copy the config shape from an existing source of the same kind (`get_source`) rather than guessing fields.
- CRM lists and campaigns: resolve ids with `lookup` (e.g. `hubspot/lists` with an `objectType` filter).
- File sources: `list_source_files` shows what was uploaded.

Test: `preview_source` with `{source: {type, ...config}, limit: 10}` and poll `get_preview`. Show the user the count and a sample (name, domain, the fields that made them match). An empty or huge result usually means a criterion is too narrow or missing.

Account search returning records outside a requested range (employee count, country) is the provider's matching, not the request; tighten the criteria or filter after the source with an expression.
