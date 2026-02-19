# agentcash Skills

Skills for accessing x402-protected APIs using the agentcash MCP.

Covers stableenrich.dev (data enrichment) and stablestudio.dev (media generation).

## Prerequisites

Install the agentcash MCP:

```bash
npx agentcash@latest install --client claude-code
```

This enables the `mcp__agentcash__*` tools used by these skills.

## Installation

Copy skills to your Claude Code skills directory:

```bash
cp -r skills/* ~/.claude/skills/
```

Or symlink for development:

```bash
ln -s $(pwd)/skills/* ~/.claude/skills/
```

## Available Skills

| Skill | Use For | Endpoints |
|-------|---------|-----------|
| [wallet](skills/wallet/) | Balance, deposits, invite codes | x402 wallet |
| [media-generation](skills/media-generation/) | AI image & video generation | StableStudio |
| [data-enrichment](skills/data-enrichment/) | Person & company profiles | Apollo, Clado |
| [web-research](skills/web-research/) | Web search & scraping | Exa, Firecrawl |
| [local-search](skills/local-search/) | Places & business info | Google Maps |
| [social-intelligence](skills/social-intelligence/) | Social media research | Grok (X/Twitter), Reddit |
| [news-shopping](skills/news-shopping/) | News & product search | Serper |
| [people-property](skills/people-property/) | People & property lookup | Whitepages |

## Quick Start

1. **Check your balance:**
   ```
   mcp__agentcash__get_wallet_info
   ```

2. **Discover available endpoints:**
   ```
   mcp__agentcash__discover_api_endpoints(url="https://stableenrich.dev")
   ```

3. **Check endpoint pricing before calling:**
   ```
   mcp__agentcash__check_endpoint_schema(url="https://stableenrich.dev/api/apollo/people-enrich")
   ```

4. **Make API calls:**
   ```
   mcp__agentcash__fetch(
     url="https://stableenrich.dev/api/apollo/people-enrich",
     method="POST",
     body={"email": "user@company.com"}
   )
   ```

## IMPORTANT: Do Not Guess Endpoints

**Never guess or invent endpoint paths.** All endpoints have specific paths that include a provider prefix:

| Wrong (guessed) | Correct |
|-----------------|---------|
| `/api/people/search` | `/api/apollo/people-search` |
| `/api/people-enrich` | `/api/apollo/people-enrich` |
| `/api/grok/search` | `/api/grok/x-search` |
| `/api/twitter/search` | `/api/grok/x-search` |

If you don't know the exact endpoint path:
1. **Use `discover_api_endpoints`** to list all available endpoints
2. **Consult the skill documentation** (SKILL.md files have correct paths)
3. **Check ENDPOINTS.md** for the complete reference

Guessing endpoints will result in `405 Method Not Allowed` errors and wasted API calls.

## Funding Your Wallet

If your balance is low:

1. Redeem an invite code: `mcp__agentcash__redeem_invite(code="YOUR_CODE")`
2. Or deposit USDC on Base to your wallet address

## All Endpoints Reference

See [ENDPOINTS.md](ENDPOINTS.md) for a complete reference of all available endpoints with pricing.
