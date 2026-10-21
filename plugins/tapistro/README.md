# Tapistro plugin for Claude

Connects Claude to [Tapistro](https://tapistro.com), a B2B go-to-market platform, through the Tapistro MCP server, and adds skills for common workflows.

## What it does

- **MCP server:** registers the remote server `https://mcp.tapistro.com/mcp`. On first use you sign in with your Tapistro account (OAuth). Every request goes only to that server and is scoped to your Tapistro tenant. The plugin runs no local code, hooks, or scripts.
- **Skills:**
  - `/tapistro:brand-context` — set up or refresh positioning, ICP, proof points and voice.
  - `/tapistro:ai-agent-builder` — design an AI Agent and test it on real records before saving.
  - `/tapistro:build-journey` — source → AI Agents → persona → destination, previewing each piece.
  - `/tapistro:journey-health` — drop-offs, failed runs and activation errors, with the fix and a re-run from the fixed step.
  - `/tapistro:in-market-accounts` — accounts ranked by intent and fit, flagging those not yet activated.
  - `/tapistro:account-brief` — one-page brief on an account.
  - `/tapistro:write-outreach` — a first-touch email or LinkedIn note grounded in Tapistro data.
  - `/tapistro:gtm-report` — period digest of pipeline, activations and credit usage.
- **GTM strategies as journeys:**
  - `/tapistro:signal-triggered-outreach` — act on buying triggers within their window, with a trigger-specific angle.
  - `/tapistro:account-tiering` — score and tier accounts, give each tier its own treatment, split tier A across reps.
  - `/tapistro:multichannel-sequence` — email + LinkedIn cadence with AI-written steps and exits on replies.
  - `/tapistro:multi-threading` — cover every buying role at an account with role-specific messages.
  - `/tapistro:re-engagement` — revive lost deals, churned customers and moved champions when something changed.

Skills pre-approve every Tapistro tool, so they run without permission prompts. Each skill asks you in the chat before creating, updating, deleting or running anything.

## Install in Claude Code

```
/plugin marketplace add tapistro/claude-plugins
/plugin install tapistro@tapistro
```

Then run `/mcp` to sign in.

## License

Proprietary. See [LICENSE](LICENSE).
