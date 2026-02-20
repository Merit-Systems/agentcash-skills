# Rate Limits and Usage Guidelines

## General Guidelines

x402 endpoints don't have strict rate limits per se, but:

1. **Respect platform norms** - Don't spam searches
2. **Space out requests** - Avoid rapid-fire identical queries
3. **Cache results** - Don't re-fetch the same data repeatedly
4. **Be specific** - Targeted queries work better than broad ones

## X/Twitter (Grok) Considerations

### Search Tips
- Use specific keywords, not generic terms
- Include usernames with @ for targeted search
- Combine terms: "AI agents" works better than just "AI"

### User Posts
- User posts are relatively stable
- Don't fetch the same user repeatedly in short timeframes
- Consider summarizing after one fetch rather than re-fetching

### What Works Well
- Keyword searches for topics
- Finding users by description
- Getting recent activity for specific accounts

### What May Not Work
- Historical searches far in the past
- Very high-volume accounts (may be truncated)
- Protected/private accounts

## Reddit Considerations

### Search Tips
- Subreddit filtering makes searches much more relevant
- Use sort="top" and time="year" for quality content
- "hot" gives current discussions

### Comments
- Large threads may have truncated comments
- Focus on top-level comments for sentiment
- Deeply nested replies may not all be returned

### What Works Well
- Subreddit-specific searches
- Top/hot post discovery
- Getting comment threads

### What May Not Work
- Very old archived posts
- Deleted content
- Quarantined subreddits

## Efficient Usage Patterns

### Do This
```bash
# One targeted search
npx agentcash fetch https://stableenrich.dev/api/reddit/search -m POST -b '{"query": "specific topic", "subreddit": "relevant", "sort": "top"}'
```

### Avoid This
```bash
# Multiple broad searches for same topic — wasteful
npx agentcash fetch https://stableenrich.dev/api/reddit/search -m POST -b '{"query": "AI"}'
npx agentcash fetch https://stableenrich.dev/api/reddit/search -m POST -b '{"query": "artificial intelligence"}'
npx agentcash fetch https://stableenrich.dev/api/reddit/search -m POST -b '{"query": "machine learning"}'
```

Instead, craft one good query or search sequentially only when each result informs the next.

## Cost Management

All social intelligence endpoints are $0.02 per call.

| Scenario | Typical Calls | Cost |
|----------|---------------|------|
| Quick check | 1 | $0.02 |
| Thorough research | 3-5 | $0.06-0.10 |
| Full monitoring | 5-10 | $0.10-0.20 |

Budget for ~10 calls per monitoring session to cover X search, user lookup, Reddit search, and comments.
