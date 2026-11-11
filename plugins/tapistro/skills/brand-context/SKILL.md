---
name: brand-context
description: Set up or refresh the company's Tapistro brand context — positioning, ICP, proof points and voice — from the user's website, docs or pasted text, then derive the brand knowledge AI Agents and outreach use. Use when asked to set Tapistro up for a company, update messaging, ICP or value props, add a deck or doc to "our brand", or when another task needs brand context and none exists.
argument-hint: "[company website, doc, or what changed]"
---

# Brand context

Input: $ARGUMENTS

A brand context is the source of truth for what the company sells, to whom, and how it sounds. AI Agents and outreach drafts read the derived knowledge, so vague input produces vague agents.

1. `list_brandContexts`. If one exists, `get_brandContext` and summarise what it already covers and whether `isStale` is true. Usually the job is to add to it, not to create a second one.
2. Gather content from the user: the website, a positioning doc, a sales deck, case studies. Turn each into one focused content item. Separate items per topic (positioning, ICP, personas, proof points, objections, voice) are better than one blob, because each can be updated alone later.
3. For each item, quote facts from the source. Never invent customer names, numbers or claims. If a topic is missing (often proof points or the ICP's disqualifiers), ask for it rather than guessing.
4. Show the user the planned items (title line + 2–3 bullet summary each) and get approval.
5. After approval: `create_brandContext` if none exists, then `add_brand_context_content` per item (or `update_brand_context_content` to replace an outdated one).
6. `derive_brand_context`. It runs in the background; `get_brandContext` later shows the derived knowledge and voice and `isStale: false` when it is done.

Report what was added or changed and what is still missing.
