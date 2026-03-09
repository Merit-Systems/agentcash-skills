---
name: social-intelligence
description: |
  Search and monitor social media using X/Twitter (via twit.sh) and Reddit APIs.

  USE FOR:
  - Searching X/Twitter posts by keywords or hashtags
  - Finding X/Twitter users by criteria
  - Getting a user's recent posts
  - Searching Reddit posts and discussions
  - Getting comments from Reddit threads
  - Social media monitoring and research

  TRIGGERS:
  - "twitter", "X", "tweets", "posts on X"
  - "reddit", "subreddit", "reddit discussion"
  - "what are people saying", "social media", "sentiment"
  - "trending", "viral", "popular posts"
  - "user's posts", "timeline", "recent activity"

  Use `npx agentcash@latest fetch` for twit.sh (X) and Reddit endpoints. X endpoints $0.005–$0.01/call; Reddit $0.02/call.

  IMPORTANT: Use exact endpoint paths from the Quick Reference table below.
---

# Social Intelligence with x402 APIs

Access X/Twitter (via twit.sh) and Reddit through x402-protected endpoints.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Description |
|------|----------|-------|-------------|
| Search X posts | `https://twit.sh/tweets/search` | $0.01 | Search tweets (GET; use `words`, `phrase`, `from`, etc.) |
| Find X users | `https://twit.sh/users/search` | $0.01 | Search users by keyword (GET; `query`) |
| Get user posts | `https://twit.sh/tweets/user` | $0.01 | User timeline (GET; `username`) |
| Look up user | `https://twit.sh/users/by/username` | $0.005 | User profile by handle (GET; `username`) |
| Search Reddit | `https://stableenrich.dev/api/reddit/search` | $0.02 | Search Reddit posts |
| Get comments | `https://stableenrich.dev/api/reddit/post-comments` | $0.02 | Comments on a post |

See [rules/rate-limits.md](rules/rate-limits.md) for usage guidance.

## X/Twitter via twit.sh

All twit.sh endpoints are GET with query parameters. Run `npx agentcash@latest discover https://twit.sh` or `npx agentcash@latest check https://twit.sh/<path>` for full parameter lists.

### Search Posts

Search for X posts by keywords (at least one filter required):

```bash
npx agentcash@latest fetch "https://twit.sh/tweets/search?words=AI%20agents"
```

**Parameters (query string):**
- `words` - All these words must appear
- `phrase` - Exact phrase match
- `anyWords` - Any of these words
- `from` - Tweets from this username
- `minLikes`, `minReplies`, `minReposts` - Engagement filters
- `since`, `until` - Date range (YYYY-MM-DD)
- `next_token` - Pagination cursor

**Returns:** Up to 20 tweets per page, X v2 API–compatible JSON (text, author, metrics, timestamps, media).

### Search Users

Find X users matching a keyword:

```bash
npx agentcash@latest fetch "https://twit.sh/users/search?query=AI%20researcher%20San%20Francisco"
```

**Parameters:**
- `query` - Search keyword or phrase (required)

**Returns:** Up to 20 user profiles per page (username, display name, bio, followers, verification, profile image).

### Get User's Posts

Fetch recent posts from a specific user:

```bash
npx agentcash@latest fetch "https://twit.sh/tweets/user?username=elonmusk"
```

**Parameters:**
- `username` - X username without @ (required)

**Returns:** Up to 20 tweets per page from the user's timeline.

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
- [ ] Use `npx agentcash@latest discover https://twit.sh` or `https://stableenrich.dev` to list endpoints
- [ ] Use `npx agentcash@latest check <endpoint-url>` to see expected parameters and pricing
- [ ] Call endpoint with `npx agentcash@latest fetch`
- [ ] Parse and present results

### Brand Monitoring

- [ ] (Optional) Check balance: `npx agentcash@latest wallet info`
- [ ] Search X for brand mentions
- [ ] Search Reddit for discussions
- [ ] Summarize sentiment and key mentions

```bash
npx agentcash@latest fetch "https://twit.sh/tweets/search?words=YourBrand&from=YourBrand"
```

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/reddit/search -m POST -b '{"query": "YourBrand", "sort": "new"}'
```

### Competitor Research

- [ ] Search Reddit for competitor reviews
- [ ] Search X for competitor mentions
- [ ] Analyze common complaints and praise

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/reddit/search -m POST -b '{"query": "competitor name review", "sort": "top", "time": "year"}'
```

### Influencer Discovery

- [ ] Define criteria (topic, follower range)
- [ ] Search for matching users
- [ ] Get recent posts for top candidates

```bash
npx agentcash@latest fetch "https://twit.sh/users/search?query=tech%20blogger%20100k%20followers"
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

### X/Twitter Post Fields
- `text` - Post content
- `author` - Username, display name, verified status
- `metrics` - Likes, retweets, replies, quotes, views
- `createdAt` - Timestamp
- `url` - Link to post
- `media` - Attached images/videos

### X/Twitter User Fields
- `username` - Handle without @
- `displayName` - Full name
- `description` - Bio
- `followers` / `following` - Counts
- `verified` - Verification status
- `profileImageUrl` - Avatar

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
| Quick X search | 1 | $0.01 |
| User profile + posts | 2 | $0.015–0.02 |
| Reddit thread + comments | 2 | $0.04 |
| Full monitoring scan | 4-6 | $0.08-0.12 |
