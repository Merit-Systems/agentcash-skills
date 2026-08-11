---
name: social-intelligence
description: |
  Search and monitor Reddit using x402-protected APIs.

  USE FOR:
  - Searching Reddit posts and discussions
  - Getting comments from Reddit threads
  - Social media monitoring and research

  TRIGGERS:
  - "reddit", "subreddit", "reddit discussion"
  - "what are people saying", "social media", "sentiment"
  - "trending", "viral", "popular posts"

  Use agentcash.fetch for Reddit endpoints. Reddit $0.02/call.

  IMPORTANT: Use exact endpoint paths from the Quick Reference table below.
mcp:
  - agentcash
metadata:
  version: 2
---

# Social Intelligence with x402 APIs

Access Reddit through x402-protected endpoints.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Description |
|------|----------|-------|-------------|
| Search Reddit | `https://stableenrich.dev/api/reddit/search` | $0.02 | Search Reddit posts |
| Get comments | `https://stableenrich.dev/api/reddit/post-comments` | $0.02 | Comments on a post |

See [rules/rate-limits.md](rules/rate-limits.md) for usage guidance.

## Reddit

### Search Posts

Search Reddit for posts:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/reddit/search",
  method="POST",
  body={
    "query": "best programming languages 2024"
  }
)
```

**Parameters:**
- `query` - Search terms (required). Scope to a subreddit with Reddit's `subreddit:` operator inside the query (there is no separate subreddit field)
- `sort` - relevance, new, top, comment_count (default: relevance)
- `timeframe` - all, day, week, month, year (default: all)
- `maxResults` - Maximum results, 1-25 (default: 10)
- `after` - Pagination cursor from a previous response

**Returns:**
- Post title and content
- Author and subreddit
- Upvotes and comment count
- Post URL

### Search in Subreddit

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/reddit/search",
  method="POST",
  body={
    "query": "typescript vs javascript subreddit:programming",
    "sort": "top",
    "timeframe": "year"
  }
)
```

### Get Post Comments

Get comments from a Reddit post:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/reddit/post-comments",
  method="POST",
  body={
    "url": "https://reddit.com/r/programming/comments/abc123/..."
  }
)
```

**Parameters:**
- `url` - Full Reddit post URL (required)
- `cursor` - Pagination cursor for more comments

**Returns:**
- Post details
- Comment text and author
- Comment scores
- Comment timestamps
- `hasMore` / `cursor` for pagination

## Workflows

### Standard

- [ ] (Optional) Check balance: `agentcash.get_balance`
- [ ] Use `agentcash.discover_api_endpoints(url="https://stableenrich.dev")` to list endpoints
- [ ] Use `agentcash.check_endpoint_schema(url="...")` to see expected parameters and pricing
- [ ] Call endpoint with `agentcash.fetch`
- [ ] Parse and present results

### Brand Monitoring

- [ ] (Optional) Check balance: `agentcash.get_balance`
- [ ] Search Reddit for discussions
- [ ] Summarize sentiment and key mentions

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/reddit/search",
  method="POST",
  body={"query": "YourBrand", "sort": "new"}
)
```

### Competitor Research

- [ ] Search Reddit for competitor reviews
- [ ] Analyze common complaints and praise

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/reddit/search",
  method="POST",
  body={"query": "competitor name review", "sort": "top", "timeframe": "year"}
)
```

### Community Sentiment

- [ ] Identify relevant subreddit
- [ ] Search for discussions on topic
- [ ] Get comments from top posts
- [ ] Synthesize overall sentiment

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/reddit/search",
  method="POST",
  body={"query": "new feature name subreddit:relevant_community", "sort": "top"}
)
```

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/reddit/post-comments",
  method="POST",
  body={"url": "https://reddit.com/..."}
)
```

## Response Data

### Reddit Post Fields
- `title` - Post title
- `selftext` - Post body (for text posts; `selftextTruncated` flags truncation)
- `author` - Username
- `subreddit` - Subreddit name
- `score` - Upvotes minus downvotes
- `numComments` - Comment count
- `permalink` - Link to post
- `createdAt` - Timestamp

Search responses also include `after` (pagination cursor) and `searchContext` (query + result count).

### Reddit Comment Fields
- `body` - Comment text (`bodyTruncated` flags truncation)
- `author` - Username
- `score` - Net upvotes
- `createdAt` - Timestamp

Comments are returned as a flat list; use `hasMore` and `cursor` to page through more.

## Cost Estimation

| Task | Calls | Cost |
|------|-------|------|
| Reddit thread + comments | 2 | $0.04 |
| Full monitoring scan | 3-5 | $0.06-0.10 |
