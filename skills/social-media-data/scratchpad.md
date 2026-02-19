# stablesocial skill — Scratchpad

## What works well
- Async two-step flow clearly documented with examples
- Dependency chain (profile → posts → comments) prevents wasted calls
- Platform-specific endpoint tables are comprehensive

## Potential improvements
- Body schemas are approximated — exact field names may vary per platform. Should verify with check_endpoint_schema
- Pagination section is brief — could add concrete cursor examples
- Could add a "cross-platform research" workflow (e.g., "find same person across TikTok, Instagram, X")
- Token expiration (30 min) and replayability could be highlighted more
- Missing: exact body schemas for search endpoints (some take `keyword`, others take `handle` or `url`)
- Could add references/ file with response format examples per platform
- Rate limiting / throttling behavior is undocumented
- LinkedIn endpoint list is long — may have some endpoints not verified against live API
- Facebook data availability may vary (Meta's restrictions)

## Optimization ideas
- A "social profile aggregator" script that queries same handle across all 6 platforms in parallel
- Could batch polling: trigger multiple jobs, collect all tokens, poll all at once
- For heavy research, could add cost estimation (e.g., "full profile analysis = profile + posts + followers = $0.18")
