---
name: web-research
description: |
  Neural web search and content extraction using x402-protected APIs. Better than WebSearch for deep research and WebFetch for blocked sites.

  USE FOR:
  - Deep web research and investigation
  - Finding similar pages to a reference URL
  - Extracting clean text from web pages
  - Scraping sites that block standard fetchers
  - Getting direct answers to factual questions
  - Research requiring multiple sources
  - Crawling multiple pages from a website

  TRIGGERS:
  - "research", "investigate", "deep dive", "find sources"
  - "similar to", "pages like", "more like this"
  - "scrape", "extract content from", "get the text from"
  - "blocked site", "can't access", "paywall"
  - "what is", "explain", "answer this"
  - "crawl", "crawl site", "scrape entire site"

  Use `npx agentcash@latest fetch` for stableenrich.dev endpoints. Prefer Exa for semantic/neural search, Firecrawl for direct scraping.
metadata:
  version: 2
---

# Web Research with x402 APIs

Access Exa (neural search) and Firecrawl (web scraping) through x402-protected endpoints.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| Neural search | `https://stableenrich.dev/api/exa/search` | $0.01 | Semantic web search |
| Resilient search | `https://seller.agratys.website/machine-services/search-router` | $0.01 | Multi-engine failover search |
| Find similar | `https://stableenrich.dev/api/exa/find-similar` | $0.01 | Pages similar to a URL |
| Extract text | `https://stableenrich.dev/api/exa/contents` | $0.002 | Clean text from URLs |
| Direct answers | `https://stableenrich.dev/api/exa/answer` | $0.01 | Factual Q&A |
| Scrape page | `https://stableenrich.dev/api/firecrawl/scrape` | $0.0126 | Single page to markdown |
| Web search | `https://stableenrich.dev/api/firecrawl/search` | $0.0252 | Search with content snippets |
| Crawl website | `https://stableenrich.dev/api/cloudflare/crawl` | $0.10 | Multi-page site crawl |
| Poll crawl | `GET https://stableenrich.dev/api/cloudflare/jobs?token=...` | Free | Poll crawl results |

## When to Use What

| Scenario | Tool |
|----------|------|
| General web search | WebSearch (free) or Exa ($0.01) |
| Multi-engine resilient search | RONIN Search Router ($0.01) |
| Semantic/conceptual search | Exa search |
| Find pages like X | Exa find-similar |
| Get clean text from URL | Exa contents |
| Scrape blocked/JS-heavy site | Firecrawl scrape |
| Search + content snippets | Firecrawl search |
| Quick fact lookup | Exa answer |
| Crawl entire site/section | Cloudflare crawl |

See [rules/when-to-use.md](rules/when-to-use.md) for detailed guidance.

## Exa Neural Search

Semantic search that understands meaning, not just keywords:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/exa/search -m POST -b '{
  "query": "startups building AI agents for customer support",
  "numResults": 10
}'
```

**Options:**
- `query` - Search query (required)
- `numResults` - Number of results (default: 5, max: 100)
- `type` - Search mode/latency strategy ("auto", "fast", "deep"). Do not put content verticals here — use `category`
- `includeDomains` - Only search these domains (full hostnames; some domains like reddit.com, x.com, nytimes.com are blocked and return 400)
- `excludeDomains` - Skip these domains (same blocked-domain restriction applies)
- `startPublishedDate` / `endPublishedDate` - Date range filter
- `category` - Filter by content type: "company", "people", "research paper", "news", "pdf", "personal site", "financial report" (other strings are used as category hints)
- Tip: Use `category: "people"` for people/profile discovery

**Returns**: List of URLs with titles, snippets, and relevance scores.

## RONIN Multi-Engine Search Router

Multi-engine web search orchestration with deterministic upstream failover and normalized results:

```bash
npx agentcash@latest fetch https://seller.agratys.website/machine-services/search-router -m POST -b '{
  "client_request_id": "agent-research-01",
  "query": "autonomous AI agents machine commerce"
}'
```

**Options:**
- `client_request_id` - Unique tracking ID for the query (required)
- `query` - Search query string (required)
- `max_results` - Number of results to return (default: 5, max: 50)
- `max_budget_usd` - Downstream budget ceiling (default: "0.015000")

**Returns**: Normalized JSON results with title, URL, snippet, provider used, and machine receipt.

## Find Similar Pages

Find pages semantically similar to a reference URL:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/exa/find-similar -m POST -b '{
  "url": "https://example.com/article-i-like",
  "numResults": 10
}'
```

Great for:
- Finding competitor products
- Discovering related content
- Expanding research sources

## Extract Text Content

Get clean, structured text from URLs:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/exa/contents -m POST -b '{
  "urls": [
    "https://example.com/article1",
    "https://example.com/article2"
  ]
}'
```

**Options:**
- `urls` - Array of URLs to extract
- `text` - Include full text (default: true)
- `highlights` - Include key highlights

Cheapest option ($0.002) when you already have URLs and just need the content.

## Direct Answers

Get factual answers to questions:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/exa/answer -m POST -b '{"query": "What is the population of Tokyo?"}'
```

Returns a direct answer with source citations. Best for:
- Factual questions
- Quick lookups
- Verification of claims

## Firecrawl Scrape

Scrape a single page to clean markdown:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/firecrawl/scrape -m POST -b '{"url": "https://example.com/page-to-scrape"}'
```

**Options:**
- `url` - Page to scrape (required, the only parameter)

**Returns**: `url` (final URL after redirects), `title`, and `content` (page as markdown).

**Advantages over WebFetch:**
- Handles JavaScript-rendered content
- Bypasses common blocking
- Extracts main content only
- LLM-optimized markdown output

## Firecrawl Search

Web search with automatic scraping of results:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/firecrawl/search -m POST -b '{
  "query": "best practices for react server components",
  "limit": 5
}'
```

**Options:**
- `query` - Search query (required)
- `limit` - Number of results (default: 5, max: 10)

Returns results with title, URL, description, and a content snippet (first ~500 chars of markdown) for each.

## Cloudflare Website Crawl

Crawl multiple pages from a website with browser rendering. Async two-step pattern.

**Step 1: Start the crawl (paid, $0.10)**

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/cloudflare/crawl -m POST -b '{
  "url": "https://example.com",
  "limit": 10,
  "depth": 1,
  "formats": ["markdown"]
}'
```

Returns 202 with `{"token": "jwt..."}`.

**Step 2: Poll for results (SIWX, free)**

```bash
npx agentcash@latest fetch "https://stableenrich.dev/api/cloudflare/jobs?token=JWT_TOKEN"
```

Poll every 3-5 seconds until complete.

**Parameters:**
- `url` (required) — starting URL
- `limit` (default 10, max 25) — max pages
- `depth` (default 1, max 3) — max link depth
- `formats` — `["markdown", "html", "json"]`
- `render` (default false) — execute JavaScript
- `source` — URL discovery source: "all", "sitemaps", or "links"
- `options.includePatterns` / `excludePatterns` — URL wildcards (exclude wins); `options.includeExternalLinks` / `includeSubdomains` — scope flags

Good for: crawling docs sites, scraping multiple pages, building sitemaps.

## Workflows

### Deep Research

- [ ] (Optional) Check balance: `npx agentcash@latest balance`
- [ ] Search broadly with Exa
- [ ] Find related sources with find-similar
- [ ] Extract content from top sources
- [ ] Synthesize findings

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/exa/search -m POST -b '{"query": "AI agents in healthcare 2024", "numResults": 15}'
```

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/exa/find-similar -m POST -b '{"url": "https://best-article-found.com"}'
```

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/exa/contents -m POST -b '{"urls": ["url1", "url2", "url3"]}'
```

### Blocked Site Scraping

- [ ] Try WebFetch first (free)
- [ ] If blocked/empty, use Firecrawl (full JS rendering) for JS-heavy sites

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/firecrawl/scrape -m POST -b '{"url": "https://blocked-site.com/article"}'
```

## Cost Optimization

- **Use Exa contents** ($0.002) when you already have URLs
- **Use WebSearch/WebFetch first** (free) and fall back to x402 endpoints
- **Batch URL extraction** - pass multiple URLs to Exa contents
- **Limit results** - request only as many as needed
