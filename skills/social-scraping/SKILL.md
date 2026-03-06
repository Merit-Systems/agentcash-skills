---
name: social-scraping
description: |
  Scrape social media profiles, posts, comments, followers, and search across 6 platforms via x402.

  USE FOR:
  - Getting TikTok, Instagram, X/Twitter, Facebook, Reddit, or LinkedIn profiles
  - Fetching a user's posts, stories, highlights, or videos
  - Getting comments, replies, and reactions on posts
  - Listing followers and following for any account
  - Searching posts, hashtags, profiles, jobs, and ads across platforms
  - Cross-platform social media research and monitoring

  TRIGGERS:
  - "tiktok", "instagram", "facebook", "linkedin profile", "linkedin posts"
  - "get followers", "who follows", "following list"
  - "scrape profile", "get posts from", "social media data"
  - "instagram stories", "tiktok videos", "facebook page"
  - "linkedin company", "linkedin jobs", "linkedin ads"
  - "cross-platform", "social media research"

  IMPORTANT: StableSocial uses an async two-step flow. Step 1: POST triggers data collection (paid, $0.06). Step 2: Poll GET /api/jobs?token=... until finished (free). All endpoints are $0.06 per call.

  Use `npx agentcash fetch` for paid POST triggers. Use `npx agentcash fetch` for free GET polling.

  IMPORTANT: Use exact endpoint paths from the Quick Reference tables below. All paths include a platform prefix (e.g. `https://stablesocial.dev/api/tiktok/...`).
---

# Social Media Scraping with StableSocial

Scrape profiles, posts, comments, followers, and search across TikTok, Instagram, X/Twitter, Facebook, Reddit, and LinkedIn. All endpoints cost $0.06 per call.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## How It Works: Async Two-Step Flow

Every request follows a **trigger-then-poll** pattern:

### Step 1: Trigger (paid, $0.06)

```bash
npx agentcash fetch https://stablesocial.dev/api/x/profile -m POST -b '{"handle": "elonmusk"}'
```

Returns `202 Accepted` with a JWT token:
```json
{"token": "eyJhbGciOiJIUzI1NiIs..."}
```

### Step 2: Poll (free)

```bash
npx agentcash fetch "https://stablesocial.dev/api/jobs?token=eyJhbGciOiJIUzI1NiIs..."
```

- `{"status": "pending"}` — poll again in 3-5 seconds
- `{"status": "finished", "data": {...}}` — data is ready
- `{"status": "failed", "error": "..."}` — collection failed (not charged)

Tokens expire after 30 minutes. Jobs typically finish in 5-60 seconds.

## Quick Reference — TikTok

| Task | Endpoint | Depends On |
|------|----------|------------|
| Get profile | `https://stablesocial.dev/api/tiktok/profile` | — |
| Get posts | `https://stablesocial.dev/api/tiktok/posts` | profile |
| Post comments | `https://stablesocial.dev/api/tiktok/post-comments` | posts |
| Comment replies | `https://stablesocial.dev/api/tiktok/comment-replies` | post-comments |
| Followers | `https://stablesocial.dev/api/tiktok/followers` | profile |
| Following | `https://stablesocial.dev/api/tiktok/following` | profile |
| Search posts | `https://stablesocial.dev/api/tiktok/search` | — |
| Search hashtag | `https://stablesocial.dev/api/tiktok/search-hashtag` | — |
| Search profiles | `https://stablesocial.dev/api/tiktok/search-profiles` | — |
| Search by music | `https://stablesocial.dev/api/tiktok/search-music` | — |

**Input:** `{"handle": "username"}` for profile/posts/followers. `{"query": "keyword"}` for search.

## Quick Reference — Instagram

| Task | Endpoint | Depends On |
|------|----------|------------|
| Get profile | `https://stablesocial.dev/api/instagram/profile` | — |
| Get posts | `https://stablesocial.dev/api/instagram/posts` | profile |
| Post comments | `https://stablesocial.dev/api/instagram/post-comments` | posts |
| Comment replies | `https://stablesocial.dev/api/instagram/comment-replies` | post-comments |
| Followers | `https://stablesocial.dev/api/instagram/followers` | profile |
| Following | `https://stablesocial.dev/api/instagram/following` | profile |
| Stories | `https://stablesocial.dev/api/instagram/stories` | profile |
| Highlights | `https://stablesocial.dev/api/instagram/highlights` | profile |
| Search posts | `https://stablesocial.dev/api/instagram/search` | — |
| Search tags | `https://stablesocial.dev/api/instagram/search-tags` | — |

**Input:** `{"handle": "username"}` for profile/posts/followers. `{"query": "keyword"}` for search.

## Quick Reference — X/Twitter

| Task | Endpoint | Depends On |
|------|----------|------------|
| Get profile | `https://stablesocial.dev/api/x/profile` | — |
| Get posts | `https://stablesocial.dev/api/x/posts` | profile |
| Post replies | `https://stablesocial.dev/api/x/post-replies` | posts |
| Post retweets | `https://stablesocial.dev/api/x/post-retweets` | posts |
| Quote tweets | `https://stablesocial.dev/api/x/post-quotes` | posts |
| Followers | `https://stablesocial.dev/api/x/followers` | profile |
| Following | `https://stablesocial.dev/api/x/following` | profile |
| Search posts | `https://stablesocial.dev/api/x/search` | — |
| Search profiles | `https://stablesocial.dev/api/x/search-profiles` | — |

**Input:** `{"handle": "username"}` for profile/posts/followers. `{"query": "keyword"}` for search.

## Quick Reference — Facebook

| Task | Endpoint | Depends On |
|------|----------|------------|
| Get profile | `https://stablesocial.dev/api/facebook/profile` | — |
| Get posts | `https://stablesocial.dev/api/facebook/posts` | profile |
| Post comments | `https://stablesocial.dev/api/facebook/post-comments` | posts |
| Comment replies | `https://stablesocial.dev/api/facebook/comment-replies` | post-comments |
| Followers | `https://stablesocial.dev/api/facebook/followers` | profile |
| Following | `https://stablesocial.dev/api/facebook/following` | profile |
| Search posts | `https://stablesocial.dev/api/facebook/search` | — |
| Search people | `https://stablesocial.dev/api/facebook/search-people` | — |
| Search pages | `https://stablesocial.dev/api/facebook/search-pages` | — |
| Search groups | `https://stablesocial.dev/api/facebook/search-groups` | — |

**Input:** `{"handle": "username"}` or `{"profile_id": "id"}` for profile. `{"query": "keyword"}` for search.

## Quick Reference — Reddit

| Task | Endpoint | Depends On |
|------|----------|------------|
| Get post | `https://stablesocial.dev/api/reddit/post` | — |
| Post comments | `https://stablesocial.dev/api/reddit/post-comments` | post |
| Get comment | `https://stablesocial.dev/api/reddit/comment` | — |
| Search posts | `https://stablesocial.dev/api/reddit/search` | — |
| Search profiles | `https://stablesocial.dev/api/reddit/search-profiles` | — |
| Subreddit posts | `https://stablesocial.dev/api/reddit/subreddit` | — |

**Input:** `{"post_id": "id"}` for post details. `{"query": "keyword"}` for search. `{"subreddit": "name"}` for subreddit.

## Quick Reference — LinkedIn

| Task | Endpoint | Depends On |
|------|----------|------------|
| Member profile | `https://stablesocial.dev/api/linkedin/profile` | — |
| Member posts | `https://stablesocial.dev/api/linkedin/posts` | profile |
| Company profile | `https://stablesocial.dev/api/linkedin/company` | — |
| Company posts | `https://stablesocial.dev/api/linkedin/company-posts` | company |
| Post comments | `https://stablesocial.dev/api/linkedin/post-comments` | posts |
| Comment replies | `https://stablesocial.dev/api/linkedin/comment-replies` | post-comments |
| Post reactors | `https://stablesocial.dev/api/linkedin/post-reactors` | posts |
| Search posts | `https://stablesocial.dev/api/linkedin/search-posts` | — |
| Search jobs | `https://stablesocial.dev/api/linkedin/search-jobs` | — |
| Search members | `https://stablesocial.dev/api/linkedin/search-members` | — |
| Search companies | `https://stablesocial.dev/api/linkedin/search-companies` | — |
| Search ads | `https://stablesocial.dev/api/linkedin/search-ads` | — |

**Input:** `{"member_id": "username"}` for profile. `{"company_id": "company"}` for company. `{"query": "keyword"}` for search.

## Data Dependencies

Some endpoints require a prior collection. For example, to get followers you must first trigger the profile:

```bash
# 1. Trigger profile collection
npx agentcash fetch https://stablesocial.dev/api/instagram/profile -m POST -b '{"handle": "natgeo"}'
# Poll until finished...

# 2. Now fetch followers (depends on profile)
npx agentcash fetch https://stablesocial.dev/api/instagram/followers -m POST -b '{"handle": "natgeo"}'
# Poll until finished...
```

## Pagination

When results are paginated, the response includes `page_info.has_next_page` and a `cursor`. Pass the cursor to fetch the next page (each page is a new paid POST):

```bash
npx agentcash fetch https://stablesocial.dev/api/tiktok/followers -m POST -b '{"handle": "username", "cursor": "abc123"}'
```

## Key Parameters

- `handle` / `profile_id` / `member_id` / `company_id` — target account
- `max_page_size` — results per page (default varies, max 100)
- `max_followers` — how many followers to collect (default 500)
- `max_posts` / `max_activities` / `max_results` — item limits (default 50)
- `cursor` — pagination cursor from previous response
- `order_by` — sort order: `date_desc`, `date_asc`, `id_desc`
- `activity_type` — LinkedIn: `posts`, `articles`, `documents`, `media`, `comments`
- `reaction_type` — LinkedIn: `LIKE`, `CELEBRATE`, `SUPPORT`, `LOVE`, `INSIGHTFUL`, `FUNNY`

## Workflows

### Profile Deep Dive

- [ ] (Optional) Check balance: `npx agentcash wallet info`
- [ ] Trigger profile collection
- [ ] Poll until finished
- [ ] Trigger posts collection
- [ ] Poll until finished
- [ ] Optionally fetch comments, followers

### Cross-Platform Search

- [ ] Search same keyword across multiple platforms
- [ ] Compare results and synthesize findings

```bash
npx agentcash fetch https://stablesocial.dev/api/x/search -m POST -b '{"query": "brand name"}'
npx agentcash fetch https://stablesocial.dev/api/instagram/search -m POST -b '{"query": "brand name"}'
npx agentcash fetch https://stablesocial.dev/api/tiktok/search -m POST -b '{"query": "brand name"}'
```

### Influencer Analysis

- [ ] Get profile on target platform
- [ ] Fetch recent posts with engagement
- [ ] Get follower list for audience analysis
- [ ] Check comments for sentiment

### Competitive Intelligence

- [ ] Search LinkedIn for competitor company
- [ ] Get company posts and reactions
- [ ] Search for competitor ads
- [ ] Monitor employee activity

```bash
npx agentcash fetch https://stablesocial.dev/api/linkedin/company -m POST -b '{"company_id": "competitor"}'
npx agentcash fetch https://stablesocial.dev/api/linkedin/search-ads -m POST -b '{"query": "competitor name"}'
```

## Cost Estimation

All endpoints are $0.06 per trigger call. Polling is free.

| Task | Calls | Cost |
|------|-------|------|
| Single profile | 1 | $0.06 |
| Profile + posts | 2 | $0.12 |
| Full profile deep dive | 4-6 | $0.24-0.36 |
| Cross-platform search (3 platforms) | 3 | $0.18 |
| Competitor analysis | 4-8 | $0.24-0.48 |

## vs social-intelligence Skill

The `social-intelligence` skill uses X/Twitter (twit.sh) and Reddit (stableenrich.dev). Use it for quick X/Twitter keyword searches and Reddit post lookups.

Use `social-scraping` (this skill) when you need:
- **TikTok, Instagram, Facebook, or LinkedIn** data
- **Profiles, followers, following** — not just search
- **Comments, replies, reactions** on posts
- **LinkedIn jobs, ads, company data**
- **Cross-platform** research
