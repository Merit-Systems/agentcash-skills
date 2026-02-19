# When to Use x402 Web Research

## Decision Tree

```
Need web info?
|
+-- Simple keyword search? --> Use WebSearch (free)
|
+-- Semantic/conceptual search? --> Use Exa search ($0.01)
|
+-- Need to scrape a specific URL?
|   |
|   +-- WebFetch works? --> Use WebFetch (free)
|   |
|   +-- Blocked/JS-heavy? --> Use Firecrawl scrape ($0.0126)
|
+-- Find similar pages? --> Use Exa find-similar ($0.01)
|
+-- Already have URLs, need text? --> Use Exa contents ($0.002)
|
+-- Quick factual answer? --> Use Exa answer ($0.01)
```

## Exa vs WebSearch

| Aspect | WebSearch | Exa Search |
|--------|-----------|------------|
| Price | Free | $0.01 |
| Search type | Keyword | Neural/semantic |
| Best for | Quick lookups | Deep research |
| Results | Web snippets | Ranked with meaning |
| Use when | Simple queries | Complex concepts |

**Use Exa when:**
- Query is conceptual ("companies solving X problem")
- Need semantic similarity
- Researching a topic in depth
- Standard search isn't finding what you need

**Use WebSearch when:**
- Query is straightforward keywords
- Quick fact check
- Don't need deep results
- Budget is a concern

## Firecrawl vs WebFetch

| Aspect | WebFetch | Firecrawl |
|--------|----------|-----------|
| Price | Free | $0.0126 |
| JS rendering | No | Yes |
| Blocked sites | Often blocked | Bypasses many |
| Output | Raw HTML | Clean markdown |
| Main content | No filtering | Extracts main content |

**Use Firecrawl when:**
- WebFetch returns blocked/403/empty
- Site uses heavy JavaScript
- Need clean markdown, not HTML
- Content is behind soft paywall

**Use WebFetch when:**
- Site works with simple fetch
- HTML format is fine
- Cost is a concern
- Testing before x402 call

## Exa Search vs Firecrawl Search

| Aspect | Exa Search | Firecrawl Search |
|--------|------------|------------------|
| Price | $0.01 | $0.0252 |
| Output | URLs + snippets | URLs + full content |
| Best for | Finding sources | Search + immediate read |

**Use Exa search** to find relevant URLs, then selectively extract content.

**Use Firecrawl search** when you want search results AND their full content in one call.

## Cost Comparison

For a typical research task (find 10 sources, read 3):

| Approach | Cost |
|----------|------|
| WebSearch + WebFetch | $0 (if sites allow) |
| Exa search + contents | $0.01 + $0.006 = $0.016 |
| Firecrawl search (5) | $0.0252 |
| Exa search + Firecrawl scrape (3) | $0.01 + $0.0378 = $0.0478 |

## Recommendation

1. **Start free**: Try WebSearch + WebFetch first
2. **Fall back to x402**: Use Exa/Firecrawl when free tools fail
3. **Exa for discovery**: Use Exa search to find URLs
4. **Cheapest extraction**: Use Exa contents ($0.002/call) for known URLs
5. **Firecrawl for tough sites**: Use when JS rendering or bypass needed
