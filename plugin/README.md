# AgentCash Plugin

Plugin that bundles the AgentCash skill with the hosted AgentCash MCP server.

## What's Included

- **AgentCash MCP server** — connects to `https://app.agentcash.dev/mcp` via `.mcp.json`
- **agentcash skill** — pay-per-call access to premium APIs via x402/MPP micropayments

## Quick Start

1. Check your balance: `agentcash.get_balance()`
2. Discover endpoints: `agentcash.discover_api_endpoints(url="https://stableenrich.dev")`
3. Make paid requests: `agentcash.fetch(url="...", method="POST", body={...})`

## Funding

Fund your AgentCash account at [app.agentcash.dev](https://app.agentcash.dev), then call `agentcash.get_balance()` to confirm the balance before paid requests.
