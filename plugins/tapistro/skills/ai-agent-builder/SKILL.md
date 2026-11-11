---
name: ai-agent-builder
description: Design, test and tune a Tapistro AI Agent — an LLM or expression that classifies, scores, tiers, answers questions about, or writes content for each account or person. Previews it on real records before saving. Use when asked to classify, score, tier or qualify accounts or contacts, build an ICP-fit agent, research something per account, or fix an agent that tags records wrongly.
argument-hint: "[what the agent should decide, e.g. 'ICP fit: A/B/C']"
---

# AI Agent builder

Goal: $ARGUMENTS

The API calls AI Agents computed attributes; their results appear on records and in journey payloads as `computed:<id>`. Talk to the user about "AI Agents".

## Pick the type

| `evaluationConfigType` | Use for | `evaluationConfig` |
|---|---|---|
| `classification` | One label from a fixed set (tier, fit, persona match) | `{possibleValues: [{name, description}], includeCitationAndReason}` |
| `expression` | A deterministic rule over existing fields; no LLM cost | `{expression}`; draft it with `build_expression_ai` |
| `questions` | Several named answers per record; always returns citations and reasoning | `{questions: [{question, name, answerType}]}` |
| `content` | Generated text (summary, opener) | `{instruction}` |
| `assignment` | Owner or bucket assignment | `{}` |

Prefer `expression` when the rule needs only existing fields: it is exact, free and instant. Use an LLM type only when the decision needs judgement or web research.

Other fields: `name`, `description` (this is the instruction the LLM follows — write it like a brief to an analyst), `targetObject` (`account`, `person`, `signal`, `activation`), `valueType` (`string`, `number`, `boolean`, `infer`), `inputAttributes` (field paths the agent reads; find them with `list_field_catalog`), optional `inputUrlAttributes` (fields holding URLs to read).

## Citations

For `classification`, set `includeCitationAndReason: true` unless the user says not to. The result then carries the label with the reasoning and sources, so users can see why a record got its label. The stored value becomes an object (`category`, `reasoning`, `__citations`) instead of a bare string. Before turning it on for an existing agent, `list_journeys` + `get_journey` to find steps whose filters reference `computed:<id>`, and show the user which ones need updating.

## Write good labels

- Each `possibleValues` entry gets a description that states the decision rule, with the evidence that puts a record there. Mutually exclusive, collectively exhaustive; include an "unknown / not enough data" value.
- Ground the `description` in the brand context (`get_brandContext` → ICP, disqualifiers) rather than generic ICP language.
- Name only the input fields the decision needs. Extra fields add cost and noise.

## Test before saving

1. Pick 3–5 records the user can judge: clear fits, clear misses, one borderline (`report_accounts` / `report_persons`).
2. `preview_aiAgent` with the full body plus `objectId` for each; poll `get_preview` until Success.
3. Show a table: record → expected → agent's answer → reasoning. Revise the description or labels where it is wrong and preview again.
4. Only when the user is happy: `create_aiAgent` (or `update_aiAgent` with the full object — updates are full replacements).

Never create or update without the user's explicit go-ahead.
