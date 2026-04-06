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
| [agentcash](skills/agentcash/) | Wallet, search, discover, and paid calls to any x402 API | x402 / MPP |
| [upload-and-share](skills/upload-and-share/) | Upload files & get public URLs | StableUpload |
| [media-generation](skills/media-generation/) | AI image & video generation | StableStudio |
| [social-scraping](skills/social-scraping/) | Scrape profiles, posts, followers across 6 platforms | StableSocial |
| [email](skills/email/) | Send emails, forwarding inboxes, custom subdomains | StableEmail |
| [phone-calls](skills/phone-calls/) | AI phone calls, buy phone numbers | StablePhone |
| [data-enrichment](skills/data-enrichment/) | Person, company, & influencer profiles; email verification | Apollo, Clado, Hunter, Influencer |
| [web-research](skills/web-research/) | Web search & scraping | Exa, Firecrawl |
| [local-search](skills/local-search/) | Places & business info | Google Maps |
| [social-intelligence](skills/social-intelligence/) | Reddit search | Reddit |
| [news-shopping](skills/news-shopping/) | News & product search | Serper |
| [people-property](skills/people-property/) | People & property lookup | Whitepages |

These skills are also available in MCP mode (the [mcp](mcp/skills/) directory). Both directories contain the same skills — each skill has a `SKILL.md` and a `rules/` directory. Choose the mode that matches your environment.

## Quick Start

### CLI mode

1. **Check your balance:**
```bash
npx agentcash@latest balance
```

2. **Search for a service** (when you don't know which origin to use):
```bash
npx agentcash@latest search "send physical mail"
```

3. **Discover available endpoints** (when you know the origin):
```bash
npx agentcash@latest discover https://stableenrich.dev
```

4. **Check endpoint pricing before calling:**
```bash
npx agentcash@latest check https://stableenrich.dev/api/apollo/people-enrich
```

5. **Make API calls:**
```bash
npx agentcash@latest fetch https://stableenrich.dev/api/apollo/people-enrich -m POST -b '{"email": "user@company.com"}'
```

### MCP mode

1. **Check your balance:**
```mcp
agentcash.get_balance()
```

If you need deposit links or per-network wallet addresses, call `agentcash.list_accounts()` separately.

2. **Search for a service** (when you don't know which origin to use):
```mcp
agentcash.search(query="send physical mail")
```

3. **Discover available endpoints** (when you know the origin):
```mcp
agentcash.discover_api_endpoints(url="https://stableenrich.dev")
```

4. **Check endpoint pricing before calling:**
```mcp
agentcash.check_endpoint_schema(url="https://stableenrich.dev/api/apollo/people-enrich")
```

5. **Make API calls:**
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

If you don't know the exact endpoint path:
1. **Use search** to find the right service by describing what you need
2. **Use discover** to list all available endpoints on a known origin
3. **Consult the skill documentation** (SKILL.md files have correct paths)

Guessing endpoints will result in `405 Method Not Allowed` errors and wasted API calls.

## Funding Your Wallet

If your balance is low:

1. Redeem an invite code: `npx agentcash@latest redeem YOUR_CODE`
2. Or run `npx agentcash@latest accounts` to get Base or Solana deposit links and wallet addresses


## Update prompts

### CLI mode

```

I want to use agentcash and all of the CLI mode skills available in the https://github.com/Merit-Systems/agentcash-skills repository.

Follow the installation instructions in the README. If you have old versions of these skills, please update them to the latest version. Please remove the outdated x402 MCP from your MCP list (or mcporter if you are using OpenClaw).

Finally, test the skills by generating an image with the media-generation skill.
```

### MCP mode

```

I want to use agentcash and all of the MCP mode skills available in the https://github.com/Merit-Systems/agentcash-skills repository.

Follow the installation instructions in the README. If you have old versions of these skills, please update them to the latest version. Please remove the outdated x402 MCP from your MCP list (or mcporter if you are using OpenClaw).

Finally, test the skills by generating an image with the media-generation skill.
```
