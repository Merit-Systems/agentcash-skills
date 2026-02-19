---
name: social-media-data
description: Get social media profiles, posts, followers, comments, stories, and search results from TikTok, Instagram, X/Twitter, Facebook, Reddit, and LinkedIn ($0.06/request). Use when asked to look up a social profile, get someone's posts, find followers, search a platform, or do social media research.
---

# StableSocial — Social Media Data

Pay-per-request social media data from 6 platforms. $0.06 per request.

## Async Two-Step Flow

Every request is async:

**Step 1: POST to trigger (paid, $0.06)**

```bash
npx agentcash fetch https://stablesocial.dev/api/x/profile -m POST -b '{"handle": "elonmusk"}'
```

Returns `202` with `{"token": "eyJ..."}`.

**Step 2: GET /api/jobs?token=... (free, no payment)**

```bash
npx agentcash fetch https://stablesocial.dev/api/jobs?token=eyJ...
```

Poll until `{"status": "finished", "data": {...}}`. Jobs finish in 5-60 seconds.

## Data Dependencies

Some endpoints require a base resource to be fetched first:

```
profile → posts → post-comments → comment-replies
profile → followers / following / stories / highlights
```

Always fetch `profile` first before requesting followers, posts, etc.

## Endpoints by Platform

All endpoints are `POST`, all cost $0.06. Body format: `{"handle": "username"}` or `{"url": "post_url"}`.

### TikTok
| Endpoint | Body | Depends on |
|---|---|---|
| `POST https://stablesocial.dev/api/tiktok/profile` | `{"handle": "..."}` | — |
| `POST https://stablesocial.dev/api/tiktok/posts` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/tiktok/post-comments` | `{"url": "..."}` | posts |
| `POST https://stablesocial.dev/api/tiktok/comment-replies` | `{"url": "..."}` | post-comments |
| `POST https://stablesocial.dev/api/tiktok/followers` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/tiktok/following` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/tiktok/search` | `{"keyword": "..."}` | — |
| `POST https://stablesocial.dev/api/tiktok/search-hashtag` | `{"hashtag": "..."}` | — |
| `POST https://stablesocial.dev/api/tiktok/search-profiles` | `{"keyword": "..."}` | — |
| `POST https://stablesocial.dev/api/tiktok/search-music` | `{"keyword": "..."}` | — |

### Instagram
| Endpoint | Body | Depends on |
|---|---|---|
| `POST https://stablesocial.dev/api/instagram/profile` | `{"handle": "..."}` | — |
| `POST https://stablesocial.dev/api/instagram/posts` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/instagram/post-comments` | `{"url": "..."}` | posts |
| `POST https://stablesocial.dev/api/instagram/comment-replies` | `{"url": "..."}` | post-comments |
| `POST https://stablesocial.dev/api/instagram/followers` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/instagram/following` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/instagram/stories` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/instagram/highlights` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/instagram/search` | `{"keyword": "..."}` | — |
| `POST https://stablesocial.dev/api/instagram/search-tags` | `{"tag": "..."}` | — |

### X/Twitter
| Endpoint | Body | Depends on |
|---|---|---|
| `POST https://stablesocial.dev/api/x/profile` | `{"handle": "..."}` | — |
| `POST https://stablesocial.dev/api/x/posts` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/x/post-replies` | `{"url": "..."}` | posts |
| `POST https://stablesocial.dev/api/x/post-retweets` | `{"url": "..."}` | posts |
| `POST https://stablesocial.dev/api/x/post-quotes` | `{"url": "..."}` | posts |
| `POST https://stablesocial.dev/api/x/followers` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/x/following` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/x/search` | `{"keyword": "..."}` | — |
| `POST https://stablesocial.dev/api/x/search-profiles` | `{"keyword": "..."}` | — |

### Facebook
| Endpoint | Body | Depends on |
|---|---|---|
| `POST https://stablesocial.dev/api/facebook/profile` | `{"handle": "..."}` | — |
| `POST https://stablesocial.dev/api/facebook/posts` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/facebook/post-comments` | `{"url": "..."}` | posts |
| `POST https://stablesocial.dev/api/facebook/comment-replies` | `{"url": "..."}` | post-comments |
| `POST https://stablesocial.dev/api/facebook/followers` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/facebook/following` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/facebook/photos` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/facebook/videos` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/facebook/search` | `{"keyword": "..."}` | — |
| `POST https://stablesocial.dev/api/facebook/search-profiles` | `{"keyword": "..."}` | — |

### Reddit
| Endpoint | Body | Depends on |
|---|---|---|
| `POST https://stablesocial.dev/api/reddit/profile` | `{"handle": "..."}` | — |
| `POST https://stablesocial.dev/api/reddit/posts` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/reddit/post-comments` | `{"url": "..."}` | posts |
| `POST https://stablesocial.dev/api/reddit/search` | `{"keyword": "..."}` | — |
| `POST https://stablesocial.dev/api/reddit/subreddit-posts` | `{"subreddit": "..."}` | — |
| `POST https://stablesocial.dev/api/reddit/subreddit-about` | `{"subreddit": "..."}` | — |

### LinkedIn
| Endpoint | Body | Depends on |
|---|---|---|
| `POST https://stablesocial.dev/api/linkedin/profile` | `{"handle": "..."}` | — |
| `POST https://stablesocial.dev/api/linkedin/posts` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/linkedin/post-comments` | `{"url": "..."}` | posts |
| `POST https://stablesocial.dev/api/linkedin/comment-replies` | `{"url": "..."}` | post-comments |
| `POST https://stablesocial.dev/api/linkedin/followers` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/linkedin/following` | `{"handle": "..."}` | profile |
| `POST https://stablesocial.dev/api/linkedin/reactions` | `{"url": "..."}` | posts |
| `POST https://stablesocial.dev/api/linkedin/company` | `{"handle": "..."}` | — |
| `POST https://stablesocial.dev/api/linkedin/company-posts` | `{"handle": "..."}` | company |
| `POST https://stablesocial.dev/api/linkedin/search` | `{"keyword": "..."}` | — |
| `POST https://stablesocial.dev/api/linkedin/search-companies` | `{"keyword": "..."}` | — |
| `POST https://stablesocial.dev/api/linkedin/search-jobs` | `{"keyword": "..."}` | — |

## Pagination

After a job finishes, use `cursor` from `page_info` in the response for the next page. Each page is a new paid POST ($0.06) which returns a new token to poll.

```json
{"handle": "username", "cursor": "next_page_cursor"}
```

Continue while `page_info.has_next_page` is `true`.

## Tips

- Tokens expire after 30 minutes but are replayable (re-fetch finished data for free)
- Always fetch profile before posts/followers/following
- Search endpoints don't require a prior profile fetch
- Run `npx agentcash discover https://stablesocial.dev` to verify exact body schemas if unsure
