# AgentCash Plugin

Claude Code plugin that bundles the AgentCash MCP server and core wallet skill for pay-per-call access to premium APIs via x402/MPP micropayments.

## Installation

```bash
claude plugin add /path/to/plugin
```

Or for local testing:

```bash
claude --plugin-dir /path/to/plugin
```

## What's Included

- **AgentCash MCP server** — automatically connected via `.mcp.json`
- **agentcash skill** — pay-per-call access to premium APIs via x402/MPP micropayments

## Quick Start

1. Check your balance: `agentcash.get_balance()`
2. Discover endpoints: `agentcash.discover_api_endpoints(url="https://stableenrich.dev")`
3. Make paid requests: `agentcash.fetch(url="...", method="POST", body={...})`

## Funding

- Redeem an invite code: `agentcash.redeem_invite(code="YOUR_CODE")`
- Get deposit links: `agentcash.list_accounts()`
