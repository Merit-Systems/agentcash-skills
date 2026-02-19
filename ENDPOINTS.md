# x402 Endpoints Reference

All endpoints at `https://stableenrich.dev/api/`. Prices include 2x markup on base cost.

> **IMPORTANT:** Do not guess endpoint paths. All endpoints include a provider prefix (e.g., `https://stableenrich.dev/api/apollo/`, `https://stableenrich.dev/api/grok/`, `https://stableenrich.dev/api/clado/`). See "Common Mistakes" at the end of this document.

## Data Enrichment (Apollo + Clado)

| Endpoint | Method | Price | Description |
|----------|--------|-------|-------------|
| `https://stableenrich.dev/api/apollo/people-enrich` | POST | $0.0495 | Enrich person by email/LinkedIn/name |
| `https://stableenrich.dev/api/apollo/people-enrich/bulk` | POST | $0.495 | Bulk enrich up to 10 people |
| `https://stableenrich.dev/api/apollo/org-enrich` | POST | $0.0495 | Enrich company by domain |
| `https://stableenrich.dev/api/apollo/org-enrich/bulk` | POST | $0.495 | Bulk enrich up to 10 companies |
| `https://stableenrich.dev/api/apollo/people-search` | POST | $0.02 | Search for people by criteria |
| `https://stableenrich.dev/api/apollo/org-search` | POST | $0.02 | Search for companies by criteria |
| `https://stableenrich.dev/api/clado/linkedin-scrape` | POST | $0.04 | Scrape full LinkedIn profile |
| `https://stableenrich.dev/api/clado/contacts-enrich` | POST | $0.20 | Find email/phone for contacts |

## Web Research (Exa + Firecrawl)

| Endpoint | Method | Price | Description |
|----------|--------|-------|-------------|
| `https://stableenrich.dev/api/exa/search` | POST | $0.01 | Neural web search |
| `https://stableenrich.dev/api/exa/find-similar` | POST | $0.01 | Find similar URLs |
| `https://stableenrich.dev/api/exa/contents` | POST | $0.002 | Extract text from URLs |
| `https://stableenrich.dev/api/exa/answer` | POST | $0.01 | Get direct answers to queries |
| `https://stableenrich.dev/api/firecrawl/scrape` | POST | $0.0126 | Scrape URL to markdown |
| `https://stableenrich.dev/api/firecrawl/search` | POST | $0.0252 | Web search with optional scraping |

## Local Search (Google Maps)

| Endpoint | Method | Price | Description |
|----------|--------|-------|-------------|
| `https://stableenrich.dev/api/google-maps/text-search/partial` | POST | $0.02 | Text search (basic fields) |
| `https://stableenrich.dev/api/google-maps/text-search/full` | POST | $0.08 | Text search (all fields + atmosphere) |
| `https://stableenrich.dev/api/google-maps/nearby-search/partial` | POST | $0.02 | Nearby search (basic fields) |
| `https://stableenrich.dev/api/google-maps/nearby-search/full` | POST | $0.08 | Nearby search (all fields) |
| `https://stableenrich.dev/api/google-maps/place-details/partial` | POST | $0.02 | Place details (basic fields) |
| `https://stableenrich.dev/api/google-maps/place-details/full` | POST | $0.05 | Place details (all fields) |

## Social Intelligence (Grok + Reddit)

| Endpoint | Method | Price | Description |
|----------|--------|-------|-------------|
| `https://stableenrich.dev/api/grok/x-search` | POST | $0.02 | Search X/Twitter posts |
| `https://stableenrich.dev/api/grok/user-search` | POST | $0.02 | Search X/Twitter users |
| `https://stableenrich.dev/api/grok/user-posts` | POST | $0.02 | Get user's recent posts |
| `https://stableenrich.dev/api/reddit/search` | POST | $0.02 | Search Reddit posts |
| `https://stableenrich.dev/api/reddit/post-comments` | POST | $0.02 | Get post comments |

## News & Shopping (Serper)

| Endpoint | Method | Price | Description |
|----------|--------|-------|-------------|
| `https://stableenrich.dev/api/serper/news` | POST | $0.04 | Google News search |
| `https://stableenrich.dev/api/serper/shopping` | POST | $0.04 | Google Shopping search |

## People & Property (Whitepages)

| Endpoint | Method | Price | Description |
|----------|--------|-------|-------------|
| `https://stableenrich.dev/api/whitepages/person-search` | POST | $0.44 | Search people by name/location |
| `https://stableenrich.dev/api/whitepages/property-search` | POST | $0.44 | Search properties |

## Common Parameters

### Field Filtering (Cost Reduction)

Most endpoints support `excludeFields` to reduce response size:

```json
{
  "email": "user@company.com",
  "excludeFields": ["employment_history", "photos"]
}
```

### Bulk Operations

Apollo bulk endpoints accept arrays of up to 10 items:

```json
{
  "people": [
    { "email": "person1@company.com" },
    { "email": "person2@company.com" }
  ]
}
```

## Using the MCP Tools

All endpoints are called via `agentcash.fetch`:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/{endpoint}",
  method="POST",
  body={ ... }
)
```

The MCP handles wallet authentication and x402 payment automatically.

## Common Mistakes

**Never guess endpoint paths.** All endpoints require the correct provider prefix.

| Incorrect (guessed) | Correct | Error |
|---------------------|---------|-------|
| `/api/people/search` | `https://stableenrich.dev/api/apollo/people-search` | 405 |
| `/api/people-enrich` | `https://stableenrich.dev/api/apollo/people-enrich` | 405 |
| `/api/org/search` | `https://stableenrich.dev/api/apollo/org-search` | 405 |
| `https://stableenrich.dev/api/grok/search` | `https://stableenrich.dev/api/grok/x-search` | 405 |
| `/api/twitter/search` | `https://stableenrich.dev/api/grok/x-search` | 405 |
| `/api/linkedin/scrape` | `https://stableenrich.dev/api/clado/linkedin-scrape` | 405 |
| `/api/maps/search` | `https://stableenrich.dev/api/google-maps/text-search/partial` | 405 |

**Wrong parameter names will also fail:**

| Incorrect | Correct |
|-----------|---------|
| `{"name": "John Doe"}` | `{"first_name": "John", "last_name": "Doe"}` |
| `{"company": "Acme"}` | `{"organization_name": "Acme"}` |
| `{"url": "..."}` | `{"linkedin_url": "..."}` |

**If unsure:**
1. Use `agentcash.discover_api_endpoints(url="https://stableenrich.dev")` to list all endpoints
2. Use `agentcash.check_endpoint_schema(url="...")` to see expected parameters
3. Consult the skill documentation (SKILL.md files)
