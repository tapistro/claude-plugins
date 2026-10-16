# Tapistro plugin for Claude

Connects Claude to [Tapistro](https://tapistro.com), a B2B go-to-market platform, through the Tapistro MCP server, and adds skills for common workflows.

## What it does

- **MCP server:** registers the remote server `https://mcp.tapistro.com/mcp`. On first use you sign in with your Tapistro account (OAuth). Every request goes only to that server and is scoped to your Tapistro tenant. The plugin runs no local code, hooks, or scripts.
- **Skills:**
  - `/tapistro:journey-performance` — step funnel, recent runs, and activation errors for a journey.
  - `/tapistro:signal-analysis` — which accounts or contacts showed signals in a period, and whether they were activated.
  - `/tapistro:build-journey` — build a journey from a recipe template, reviewing the payload before anything is created.
  - `/tapistro:debug-activation` — find why records did not reach a destination.

Skills pre-approve only read-only tools. Creating, updating, or deleting anything still asks for your confirmation.

## Install in Claude Code

```
/plugin marketplace add tapistro/claude-plugins
/plugin install tapistro@tapistro
```

Then run `/mcp` to sign in.

## License

Proprietary. See [LICENSE](LICENSE).
