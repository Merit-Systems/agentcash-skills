---
name: news-shopping
description: |
  Search Google News, Google Shopping, Google Images, and Google Lens using Serper APIs via x402.

  USE FOR:
  - Finding recent news articles on a topic
  - Current events and breaking news
  - Product searches and price comparisons
  - Shopping research and product discovery
  - Industry news monitoring
  - Image search and finding photos/headshots of people
  - Reverse image search (Google Lens)

  TRIGGERS:
  - "news about", "latest news", "recent articles"
  - "current events", "breaking", "headlines"
  - "shopping", "buy", "price", "product search"
  - "compare prices", "where to buy", "deals on"
  - "image of", "photo of", "headshot", "reverse image search"

  Use agentcash.fetch for Serper endpoints. Most endpoints are $0.04 per call; Lens is $0.20.
mcp:
  - agentcash
metadata:
  version: 2
---

# News & Shopping Search with Serper

Access Google News, Google Shopping, Google Images, and Google Lens through x402-protected endpoints.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Description |
|------|----------|-------|-------------|
| News search | `https://stableenrich.dev/api/serper/news` | $0.04 | Google News search |
| Shopping search | `https://stableenrich.dev/api/serper/shopping` | $0.04 | Google Shopping search |
| Image search | `https://stableenrich.dev/api/serper/images` | $0.04 | Google Images search |
| People image search | `https://stableenrich.dev/api/serper/people-image-search` | $0.04 | Google Images tuned for people/headshots |
| Reverse image search | `https://stableenrich.dev/api/serper/lens` | $0.20 | Google Lens reverse image search |

## News Search

Search Google News for articles:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/serper/news",
  method="POST",
  body={
    "q": "artificial intelligence regulation"
  }
)
```

**Parameters:**
- `q` - Search query (required)
- `num` - Number of results (1-100, default: 10)
- `gl` - Country code (e.g., "us", "uk", "de")
- `hl` - Language (e.g., "en", "es", "fr")
- `location` - Location (e.g., "New York, NY")
- `tbs` - Time filter (qdr:h, qdr:d, qdr:w, qdr:m, qdr:y)

### Time Filters

| Filter | Meaning |
|--------|---------|
| `qdr:h` | Past hour |
| `qdr:d` | Past 24 hours |
| `qdr:w` | Past week |
| `qdr:m` | Past month |
| `qdr:y` | Past year |

Example - news from past week:

```mcp
agentcash.fetch(
  url=".../serper/news",
  body={
    "q": "AI startups funding",
    "tbs": "qdr:w"
  }
)
```

### Country/Language Filtering

```mcp
agentcash.fetch(
  url=".../serper/news",
  body={
    "q": "technology news",
    "gl": "uk",
    "hl": "en"
  }
)
```

**Returns:**
- Article title and snippet
- Source/publication name
- Published date
- Article URL
- Thumbnail image (if available)

## Shopping Search

Search Google Shopping for products:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/serper/shopping",
  method="POST",
  body={
    "q": "wireless noise cancelling headphones"
  }
)
```

**Parameters:**
- `q` - Search query (required)
- `num` - Number of results (1-100, default: 10)
- `gl` - Country code for pricing/availability
- `hl` - Language
- `location` - Location (e.g., "New York, NY")

**Returns:**
- Product title
- Price and currency
- Merchant/store name
- Product URL
- Rating (if available)
- Thumbnail image

### Shopping with Location

Get local pricing and availability:

```mcp
agentcash.fetch(
  url=".../serper/shopping",
  body={
    "q": "MacBook Pro M3",
    "gl": "us"
  }
)
```

## Image Search

Search Google Images:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/serper/images",
  method="POST",
  body={
    "q": "aurora borealis over mountains"
  }
)
```

**Parameters:**
- `q` - Search query (required)
- `num` - Number of results (1-100, default: 10)
- `gl` - Country code
- `hl` - Language
- `location` - Location (e.g., "New York, NY")

**Returns:**
- Image title
- Full image URL with width/height
- Thumbnail URL with width/height
- Source page name, domain, and link

## People Image Search

Google Images tuned for finding people/headshots:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/serper/people-image-search",
  method="POST",
  body={
    "q": "\"Jane Doe\" \"Acme\" LinkedIn headshot"
  }
)
```

**Parameters:** Same as Image Search. Use an exact-name + company/context query.

Tips:
- For ambiguous people, resolve the profile slug/company/title first
- LinkedIn photos may be non-public; if the first search fails, retry with public non-LinkedIn source terms

## Reverse Image Search (Lens)

Reverse-search a public image URL with Google Lens ($0.20 per call):

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/serper/lens",
  method="POST",
  body={
    "url": "https://example.com/photo.jpg"
  }
)
```

**Parameters:**
- `url` - Public URL of the image to reverse-search (required)
- `gl` - Country code
- `hl` - Language

**Returns:** `organic` matches with title, link, source, image/thumbnail URLs, price/priceRange (if available), and snippet.

## Workflows

### News Monitoring

- [ ] (Optional) Check balance: `agentcash.get_balance`
- [ ] Search with appropriate time filter
- [ ] Review and summarize top stories

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/serper/news",
  method="POST",
  body={"q": "company name OR competitor name", "tbs": "qdr:d", "num": 20}
)
```

### Breaking News Research

- [ ] Search with `qdr:h` for past hour
- [ ] Identify key sources and facts
- [ ] Note developing aspects

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/serper/news",
  method="POST",
  body={"q": "breaking news topic", "tbs": "qdr:h"}
)
```

### Product Research

- [ ] Define search criteria (category, price range)
- [ ] Search Google Shopping
- [ ] Compare prices across merchants
- [ ] Present top options with pros/cons

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/serper/shopping",
  method="POST",
  body={"q": "ergonomic office chair under $500", "num": 20}
)
```

### Price Comparison

- [ ] Use exact product name/model
- [ ] Compare merchant prices
- [ ] Note shipping and availability

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/serper/shopping",
  method="POST",
  body={"q": "exact product name model number"}
)
```

## Response Data

### News Article Fields
- `title` - Article headline
- `snippet` - Article excerpt
- `source` - Publication name
- `date` - Published date
- `link` - Article URL
- `imageUrl` - Thumbnail (if available)

### Shopping Product Fields
- `title` - Product name
- `price` - Price with currency
- `source` - Merchant/store
- `link` - Product URL
- `rating` - Star rating (if available)
- `reviews` - Number of reviews
- `imageUrl` - Product image
- `delivery` - Shipping info (if available)

## Tips

### News Search
- Use quotes for exact phrases: `"climate change"`
- Use OR for alternatives: `AI OR "artificial intelligence"`
- Be specific to get relevant results
- Use time filters to avoid old news

### Shopping Search
- Include brand/model for specific products
- Add "under $X" or "best" for filtered results
- Check `gl` parameter for accurate local pricing
- More results = better price comparison

## Cost Estimation

News, shopping, images, and people-image-search are $0.04 per call; Lens is $0.20.

| Task | Calls | Cost |
|------|-------|------|
| Quick news check | 1 | $0.04 |
| Daily news summary | 2-3 | $0.08-0.12 |
| Product research | 1-2 | $0.04-0.08 |
| Full market research | 3-5 | $0.12-0.20 |
| Image or headshot search | 1 | $0.04 |
| Reverse image lookup (Lens) | 1 | $0.20 |
