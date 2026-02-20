# agentcash Skills

Skills for accessing premium, x402-protected APIs using the agentcash CLI or MCP.

## Installation

### CLI mode (recommended)

```bash
npx skills add Merit-Systems/agentcash-skills --all --yes
```

After installing, restart your agent and start a new chat:

```
What can I do with with agentcash?
```

### MCP mode

First install the agentcash MCP server:

```bash
npx agentcash@latest install    # interactive
npx agentcash@latest install -y # non-interactive (for agents)
```

Then install the skills:

```bash
npx skills add Merit-Systems/agentcash-skills/mcp --all --yes
```

## Available Skills

| Skill | Use For | Endpoints |
|-------|---------|-----------|
| [agentcash-wallet](skills/agentcash-wallet/) | Balance, deposits, invite codes, discovering & calling any x402 API | x402 wallet |
| [upload-and-share](skills/upload-and-share/) | Upload files & get public URLs | StableUpload |
| [media-generation](skills/media-generation/) | AI image & video generation | StableStudio |
| [social-scraping](skills/social-scraping/) | Scrape profiles, posts, followers across 6 platforms | StableSocial |
| [email](skills/email/) | Send emails, forwarding inboxes, custom subdomains | StableEmail |
| [phone-calls](skills/phone-calls/) | AI phone calls, buy phone numbers | StablePhone |
| [data-enrichment](skills/data-enrichment/) | Person, company, & influencer profiles; email verification | Apollo, Clado, Hunter, Influencer |
| [web-research](skills/web-research/) | Web search & scraping | Exa, Firecrawl |
| [local-search](skills/local-search/) | Places & business info | Google Maps |
| [social-intelligence](skills/social-intelligence/) | X/Twitter & Reddit search | Grok, Reddit |
| [news-shopping](skills/news-shopping/) | News & product search | Serper |
| [people-property](skills/people-property/) | People & property lookup | Whitepages |

These skills are also available in MCP mode (the [mcp](mcp/skills/) directory). Both directories contain the same skills — each skill has a `SKILL.md` and a `rules/` directory. Choose the mode that matches your environment.

## Quick Start

### CLI mode

1. **Check your balance:**
```bash
npx agentcash wallet info
```

2. **Discover available endpoints:**
```bash
npx agentcash discover https://stableenrich.dev
```

3. **Check endpoint pricing before calling:**
```bash
npx agentcash check https://stableenrich.dev/api/apollo/people-enrich
```

4. **Make API calls:**
```bash
npx agentcash fetch https://stableenrich.dev/api/apollo/people-enrich -m POST -b '{"email": "user@company.com"}'
```

### MCP mode

1. **Check your balance:**
```mcp
agentcash.get_wallet_info()
```

2. **Discover available endpoints:**
```mcp
agentcash.discover_api_endpoints(url="https://stableenrich.dev")
```

3. **Check endpoint pricing before calling:**
```mcp
agentcash.check_endpoint_schema(url="https://stableenrich.dev/api/apollo/people-enrich")
```

4. **Make API calls:**
```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/apollo/people-enrich",
  method="POST",
  body={"email": "user@company.com"}
)
```

## IMPORTANT: Do Not Guess Endpoints

**Never guess or invent endpoint paths.** All endpoints have specific paths that include a provider prefix:

| Wrong (guessed) | Correct |
|-----------------|---------|
| `/api/people/search` | `https://stableenrich.dev/api/apollo/people-search` |
| `/api/people-enrich` | `https://stableenrich.dev/api/apollo/people-enrich` |
| `https://stableenrich.dev/api/grok/search` | `https://stableenrich.dev/api/grok/x-search` |
| `/api/twitter/search` | `https://stableenrich.dev/api/grok/x-search` |

If you don't know the exact endpoint path:
1. **Use discover** to list all available endpoints
2. **Consult the skill documentation** (SKILL.md files have correct paths)

Guessing endpoints will result in `405 Method Not Allowed` errors and wasted API calls.

## Funding Your Wallet

If your balance is low:

1. Redeem an invite code: `npx agentcash wallet redeem YOUR_CODE`
2. Or deposit USDC on Base to your wallet address
