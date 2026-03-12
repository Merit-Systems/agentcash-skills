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

  Use `npx agentcash@latest fetch` for Reddit endpoints. Reddit $0.02/call.

  IMPORTANT: Use exact endpoint paths from the Quick Reference table below.
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

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/reddit/search -m POST -b '{"query": "best programming languages 2024"}'
```

**Parameters:**
- `query` - Search terms (required)
- `subreddit` - Limit to specific subreddit
- `sort` - relevance, hot, new, top
- `time` - hour, day, week, month, year, all

**Returns:**
- Post title and content
- Author and subreddit
- Upvotes and comment count
- Post URL

### Search in Subreddit

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/reddit/search -m POST -b '{
  "query": "typescript vs javascript",
  "subreddit": "programming",
  "sort": "top",
  "time": "year"
}'
```

### Get Post Comments

Get comments from a Reddit post:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/reddit/post-comments -m POST -b '{"postUrl": "https://reddit.com/r/programming/comments/abc123/..."}'
```

**Returns:**
- Comment text and author
- Upvotes/downvotes
- Reply threads
- Comment timestamps

## Workflows

### Standard

- [ ] (Optional) Check balance: `npx agentcash@latest wallet info`
- [ ] Use `npx agentcash@latest discover https://stableenrich.dev` to list endpoints
- [ ] Use `npx agentcash@latest check <endpoint-url>` to see expected parameters and pricing
- [ ] Call endpoint with `npx agentcash@latest fetch`
- [ ] Parse and present results

### Brand Monitoring

- [ ] (Optional) Check balance: `npx agentcash@latest wallet info`
- [ ] Search Reddit for discussions
- [ ] Summarize sentiment and key mentions

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/reddit/search -m POST -b '{"query": "YourBrand", "sort": "new"}'
```

### Competitor Research

- [ ] Search Reddit for competitor reviews
- [ ] Analyze common complaints and praise

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/reddit/search -m POST -b '{"query": "competitor name review", "sort": "top", "time": "year"}'
```

### Community Sentiment

- [ ] Identify relevant subreddit
- [ ] Search for discussions on topic
- [ ] Get comments from top posts
- [ ] Synthesize overall sentiment

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/reddit/search -m POST -b '{"query": "new feature name", "subreddit": "relevant_community", "sort": "hot"}'
```

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/reddit/post-comments -m POST -b '{"postUrl": "https://reddit.com/..."}'
```

## Response Data

### Reddit Post Fields
- `title` - Post title
- `selftext` - Post body (for text posts)
- `author` - Username
- `subreddit` - Subreddit name
- `score` - Upvotes minus downvotes
- `numComments` - Comment count
- `url` - Link to post
- `createdUtc` - Timestamp

### Reddit Comment Fields
- `body` - Comment text
- `author` - Username
- `score` - Net upvotes
- `replies` - Nested replies
- `createdUtc` - Timestamp

## Cost Estimation

| Task | Calls | Cost |
|------|-------|------|
| Reddit thread + comments | 2 | $0.04 |
| Full monitoring scan | 3-5 | $0.06-0.10 |
