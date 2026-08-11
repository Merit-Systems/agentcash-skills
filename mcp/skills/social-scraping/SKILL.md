---
name: social-scraping
description: |
  Scrape social media profiles, posts, comments, followers, and search across 20+ platforms via x402.

  USE FOR:
  - Getting TikTok, Instagram, YouTube, LinkedIn, X/Twitter, Facebook, or Reddit profiles
  - Fetching a user's posts, stories, highlights, videos, or transcripts
  - Getting comments, replies, and reactions on posts
  - Listing followers and following for any account
  - Searching posts, hashtags, and profiles across platforms
  - Scraping ad libraries (Facebook, TikTok, Google, LinkedIn), Facebook Marketplace, Events, and Groups
  - GitHub users, repos, and trending; Rumble, Threads, Bluesky, Pinterest, Twitch, Spotify, and more
  - UGC research via the Lightreel agent: hooks, trends, creator search, scripts, briefs
  - Cross-platform social media research and monitoring

  TRIGGERS:
  - "tiktok", "instagram", "facebook", "reddit", "youtube", "linkedin", "twitter"
  - "get followers", "who follows", "following list"
  - "scrape profile", "get posts from", "social media data"
  - "instagram stories", "tiktok videos", "facebook page", "youtube channel"
  - "ad library", "facebook ads", "tiktok ads", "marketplace"
  - "ugc", "hooks", "creator search", "content trends"
  - "cross-platform", "social media research"

  IMPORTANT: StableSocial uses an async two-step flow. Step 1: POST triggers data collection (paid, $0.06). Step 2: Poll GET /api/jobs/{jobId} (or legacy GET /api/jobs?token=...) until finished (free). All endpoints are $0.06 per call.
mcp:
  - agentcash
metadata:
  version: 2
---

# Social Media Scraping with StableSocial

Scrape profiles, posts, comments, followers, and search across 20+ platforms: TikTok, Instagram, YouTube, LinkedIn, X/Twitter, Facebook, Reddit, GitHub, Rumble, Threads, Bluesky, Pinterest, Twitch, Spotify, and more — plus ad libraries and a UGC research agent (Lightreel). All endpoints cost $0.06 per call.

Three endpoint families:
- **Legacy platform endpoints** (`/api/tiktok/*`, `/api/instagram/*`, `/api/facebook/*`, `/api/reddit/*`) — profiles, posts, comments, followers, search
- **Scrape Creators suite** (`/api/sc/*`) — much broader platform and data coverage, including transcripts, ad libraries, and niche platforms
- **Lightreel UGC research agent** (`/api/lightreel/*`) — durable-job research tasks (hooks, trends, creator search, scripts, briefs)

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Notes
Use `agentcash.fetch` for both the paid POST trigger and the free GET polling step.

IMPORTANT: Use exact endpoint paths from the Quick Reference tables below. All paths include a platform prefix (e.g. `https://stablesocial.dev/api/tiktok/...`).

## How It Works: Async Two-Step Flow

Every request follows a **trigger-then-poll** pattern:

### Step 1: Trigger (paid, $0.06)

```mcp
agentcash.fetch(
  url="https://stablesocial.dev/api/instagram/profile",
  method="POST",
  body={"handle": "natgeo"}
)
```

Returns `202 Accepted` with a durable job ID, poll URL, and legacy JWT token:
```json
{"jobId": "abc123", "status": "pending", "pollUrl": "https://stablesocial.dev/api/jobs/abc123", "token": "eyJhbGciOiJIUzI1NiIs..."}
```

### Step 2: Poll (free)

Poll the durable job by ID (requires SIWX wallet auth from the paying wallet — `agentcash.fetch` handles this automatically):

```mcp
agentcash.fetch(
  url="https://stablesocial.dev/api/jobs/abc123",
  method="GET"
)
```

Or poll with the legacy token (no wallet auth needed):

```mcp
agentcash.fetch(
  url="https://stablesocial.dev/api/jobs?token=eyJhbGciOiJIUzI1NiIs...",
  method="GET"
)
```

- `{"status": "pending"}` — poll again in 3-5 seconds
- `{"status": "finished", "data": {...}}` — data is ready
- `{"status": "failed", "error": "..."}` — collection failed (not charged)

Tokens expire after 30 minutes; durable jobs remain pollable by jobId. Jobs typically finish in 5-60 seconds (Lightreel jobs can take longer). `GET /api/jobs` (no token) lists your durable jobs.

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

## Quick Reference — Scrape Creators Suite (`/api/sc/*`)

A larger, newer suite (~155 endpoints, all $0.06, same trigger-then-poll flow) covering many more platforms and data types. Coverage by platform:

| Platform | Base path | Coverage highlights |
|----------|-----------|---------------------|
| TikTok | `https://stablesocial.dev/api/sc/tiktok/...` | profile, videos, video transcripts, comments/replies, followers/following, audience demographics, search (users/hashtag/keyword/top), trending feed, popular creators/hashtags, songs, Shop products/reviews |
| Instagram | `https://stablesocial.dev/api/sc/instagram/...` | profile, posts, reels, post/reel info, media transcripts, comments, highlights, hashtag/profile/reels search, trending reels |
| YouTube | `https://stablesocial.dev/api/sc/youtube/...` | channel details/videos/playlists/lives/shorts/community posts, video details, transcripts, sponsors, comments/replies, search, trending shorts |
| LinkedIn | `https://stablesocial.dev/api/sc/linkedin/...` | person profile (pass a linkedin.com/in/... URL), company page/posts, post details/transcript, search posts, ads search/details |
| Facebook | `https://stablesocial.dev/api/sc/facebook/...` | profile, posts/reels/photos, post transcript, comments/replies, group posts, Ad Library (search/details/transcript/company ads), Marketplace (search/item/location), Events (search/details) |
| X/Twitter | `https://stablesocial.dev/api/sc/twitter/...` | profile, user tweets, tweet details/transcript, communities |
| Reddit | `https://stablesocial.dev/api/sc/reddit/...` | subreddit details/posts/search, post comments/transcript, search |
| GitHub | `https://stablesocial.dev/api/sc/github/...` | user, repositories, PRs, activity, followers/following, contributions, repository, trending repos/developers |
| Rumble | `https://stablesocial.dev/api/sc/rumble/...` | search, channel videos, video, transcript, comments |
| Ad libraries | `.../api/sc/tiktok/ad-library/...`, `.../api/sc/google/...`, `.../api/sc/linkedin/ads/...` | TikTok Ad Library search/ad, Google company ads/ad details/advertiser search, LinkedIn ads |
| Others | `https://stablesocial.dev/api/sc/...` | Truth Social, Threads, Bluesky, Pinterest, Twitch, Spotify, SoundCloud, Kwai, Kick, Snapchat, Google Search, Amazon Shop, link-in-bio pages (Linktree, Komi, Pillar, Linkbio, Linkme), age/gender detection |

Representative examples:

```mcp
agentcash.fetch(url="https://stablesocial.dev/api/sc/tiktok/profile", method="POST", body={"handle": "username"})
agentcash.fetch(url="https://stablesocial.dev/api/sc/youtube/video/transcript", method="POST", body={"url": "https://youtube.com/watch?v=..."})
agentcash.fetch(url="https://stablesocial.dev/api/sc/linkedin/profile", method="POST", body={"url": "https://linkedin.com/in/someone"})
agentcash.fetch(url="https://stablesocial.dev/api/sc/facebook/adLibrary/search/ads", method="POST", body={"query": "brand name"})
```

**Input:** varies per endpoint — many accept `{"handle": ...}` or an ID, some accept a full `{"url": ...}` (e.g. LinkedIn profile). Check the endpoint schema with `agentcash.check_endpoint_schema` when unsure.

## Quick Reference — Lightreel UGC Research Agent (`/api/lightreel/*`)

An AI research agent for UGC/short-form content. All $0.06 per call; returns a durable job immediately — poll `GET /api/jobs/{jobId}`. Jobs are research tasks and can take minutes, not seconds.

| Task | Endpoint |
|------|----------|
| Ask the agent anything | `https://stablesocial.dev/api/lightreel/chat` |
| Top-performing hooks | `https://stablesocial.dev/api/lightreel/top-hooks` |
| UGC video search | `https://stablesocial.dev/api/lightreel/video-search` |
| Creator search (incl. contact info) | `https://stablesocial.dev/api/lightreel/creator-search` |
| Content trends | `https://stablesocial.dev/api/lightreel/trends` |
| Account audit/feedback | `https://stablesocial.dev/api/lightreel/account-feedback` |
| Competitor strategy | `https://stablesocial.dev/api/lightreel/competitor-strategy` |
| Script ideas | `https://stablesocial.dev/api/lightreel/script-ideas` |
| Video ideas | `https://stablesocial.dev/api/lightreel/video-ideas` |
| Video/draft feedback | `https://stablesocial.dev/api/lightreel/video-feedback` |
| UGC brief | `https://stablesocial.dev/api/lightreel/ugc-brief` |
| Content calendar | `https://stablesocial.dev/api/lightreel/content-calendar` |
| Brand mentions | `https://stablesocial.dev/api/lightreel/brand-mentions` |
| Creator performance | `https://stablesocial.dev/api/lightreel/creator-performance` |

**Input:** `/chat` takes `{"question": "..."}` (required), optional `conversation_id` to continue a conversation and `response_fields` (up to 5 fields, type `string` or `array`) for structured output. Task endpoints take task-specific fields — e.g. `/top-hooks` takes `{"topic": "..."}` (required) plus optional `timeframe`, `platform`, `max_hooks`, `conversation_id`.

```mcp
agentcash.fetch(url="https://stablesocial.dev/api/lightreel/top-hooks", method="POST", body={"topic": "skincare UGC ads"})
```

Free SIWX-authed extras: `GET /api/lightreel/chats` lists your Lightreel chats; `GET /api/lightreel/chat/{conversationId}` fetches a transcript (must be the wallet that paid).

## Data Dependencies

Some endpoints require a prior collection. For example, to get followers you must first trigger the profile:

```mcp
# 1. Trigger profile collection
agentcash.fetch(
  url="https://stablesocial.dev/api/instagram/profile",
  method="POST",
  body={"handle": "natgeo"}
)
# Poll until finished...

# 2. Now fetch followers (depends on profile)
agentcash.fetch(
  url="https://stablesocial.dev/api/instagram/followers",
  method="POST",
  body={"handle": "natgeo"}
)
# Poll until finished...
```

## Pagination

When results are paginated, the response includes `page_info.has_next_page` and a `cursor`. Pass the cursor to fetch the next page (each page is a new paid POST):

```mcp
agentcash.fetch(
  url="https://stablesocial.dev/api/tiktok/followers",
  method="POST",
  body={"handle": "username", "cursor": "abc123"}
)
```

## Key Parameters

- `handle` / `profile_id` — target account
- `max_page_size` — results per page (default varies, max 100)
- `max_followers` — how many followers to collect (default 500)
- `max_posts` / `max_results` — item limits (default 50)
- `cursor` — pagination cursor from previous response
- `order_by` — sort order: `date_desc`, `date_asc`, `id_desc`

## Workflows

### Profile Deep Dive

- [ ] (Optional) Check balance: `agentcash.get_balance`
- [ ] Trigger profile collection
- [ ] Poll until finished
- [ ] Trigger posts collection
- [ ] Poll until finished
- [ ] Optionally fetch comments, followers

### Cross-Platform Search

- [ ] Search same keyword across multiple platforms
- [ ] Compare results and synthesize findings

```mcp
agentcash.fetch(url="https://stablesocial.dev/api/instagram/search", method="POST", body={"query": "brand name"})
agentcash.fetch(url="https://stablesocial.dev/api/tiktok/search", method="POST", body={"query": "brand name"})
agentcash.fetch(url="https://stablesocial.dev/api/reddit/search", method="POST", body={"query": "brand name"})
```

### Influencer Analysis

- [ ] Get profile on target platform
- [ ] Fetch recent posts with engagement
- [ ] Get follower list for audience analysis
- [ ] Check comments for sentiment

### Competitive Intelligence

- [ ] Search for competitor on target platform
- [ ] Get competitor posts and engagement
- [ ] Monitor competitor mentions across platforms
- [ ] Analyze audience sentiment via comments

```mcp
agentcash.fetch(url="https://stablesocial.dev/api/instagram/profile", method="POST", body={"handle": "competitor"})
agentcash.fetch(url="https://stablesocial.dev/api/reddit/search", method="POST", body={"query": "competitor name"})
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

The `social-intelligence` skill uses Reddit (stableenrich.dev). Use it for quick Reddit post lookups and discussions.

Use `social-scraping` (this skill) when you need:
- **TikTok, Instagram, YouTube, LinkedIn, X/Twitter, Facebook, Reddit, GitHub, or other platform** data (beyond basic Reddit search)
- **Profiles, followers, following** — not just search
- **Comments, replies, reactions, transcripts** on posts and videos
- **Ad libraries, Marketplace, Events** data
- **UGC research** (hooks, trends, creators, scripts) via Lightreel
- **Cross-platform** research
