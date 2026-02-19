---
name: research-and-enrich
description: Research and enrich people, companies, and data from 12+ providers — Apollo (people/org search), Exa (web search), Firecrawl (web scraping), Grok (X/Twitter), Google Maps (places), Clado (LinkedIn), Serper (news/shopping), WhitePages (people/property), Reddit, Hunter (email verification), and influencer data. Use when asked to look up a person, research a company, scrape a website, search Twitter, find places, verify an email, enrich contacts, or build prospect lists.
---

# StableEnrich — Research & Data APIs

Pay-per-request access to 12+ data providers. No API keys, no subscriptions.

## Quick Start

```bash
npx agentcash discover https://stableenrich.dev
```

Then fetch any endpoint:

```bash
npx agentcash fetch https://stableenrich.dev/api/apollo/people-search -m POST -b '{"person_titles": ["CEO"], "person_locations": ["San Francisco"]}'
```

Always run `discover` first to read the `instructions` field.

## Provider Guide

### Apollo — People & Company Data ($0.02-$0.495)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/apollo/people-search` | $0.02 | Find prospects by title, location, company |
| `POST https://stableenrich.dev/api/apollo/org-search` | $0.02 | Find companies by domain, name, industry |
| `POST https://stableenrich.dev/api/apollo/people-enrich` | $0.0495 | Full profile from email/name/domain |
| `POST https://stableenrich.dev/api/apollo/people-enrich/bulk` | $0.495 | Enrich up to 10 people at once |
| `POST https://stableenrich.dev/api/apollo/org-enrich` | $0.0495 | Full company profile from domain |
| `POST https://stableenrich.dev/api/apollo/org-enrich/bulk` | $0.495 | Enrich up to 10 companies at once |

**Critical workflow:**
1. `people-search` returns **obfuscated names** — always follow up with `people-enrich` using the person's ID
2. Before searching people at a company, verify the org with `org-search` first (Apollo uses exact ID matching)
3. If Apollo lacks data (especially personal emails), fall back to Clado

### Exa — Semantic Web Search ($0.002-$0.01)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/exa/search` | $0.01 | Neural search across the web |
| `POST https://stableenrich.dev/api/exa/find-similar` | $0.01 | Find pages similar to a URL |
| `POST https://stableenrich.dev/api/exa/contents` | $0.002 | Get full content from URLs (cheapest scraper) |
| `POST https://stableenrich.dev/api/exa/answer` | $0.01 | AI-generated answer with citations |

### Firecrawl — Web Scraping ($0.0126-$0.0252)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/firecrawl/scrape` | $0.0126 | Scrape any URL to clean markdown |
| `POST https://stableenrich.dev/api/firecrawl/search` | $0.0252 | Search + scrape results |

**Best scraper for single pages.** For bulk URL content, use Exa contents ($0.002) instead.

### Grok — X/Twitter Data ($0.02)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/grok/x-search` | $0.02 | Search X/Twitter posts |
| `POST https://stableenrich.dev/api/grok/user-search` | $0.02 | Find users by name/handle/bio |
| `POST https://stableenrich.dev/api/grok/user-posts` | $0.02 | Get recent posts from a user |

### Google Maps — Places ($0.02-$0.08)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/google-maps/text-search/full` | $0.08 | Search places with full details |
| `POST https://stableenrich.dev/api/google-maps/text-search/partial` | $0.02 | Search places (basic info only) |
| `POST https://stableenrich.dev/api/google-maps/nearby-search/full` | $0.08 | Find nearby places with full details |
| `POST https://stableenrich.dev/api/google-maps/nearby-search/partial` | $0.02 | Find nearby places (basic info) |
| `GET https://stableenrich.dev/api/google-maps/place-details/full?placeId=...` | $0.05 | Full details for a place ID |
| `GET https://stableenrich.dev/api/google-maps/place-details/partial?placeId=...` | $0.02 | Basic details for a place ID |

Start with `partial` endpoints ($0.02) unless ratings/reviews/contact info are needed.

### Clado — LinkedIn & Contacts ($0.04-$0.20)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/clado/linkedin-scrape` | $0.04 | Scrape full LinkedIn profile |
| `POST https://stableenrich.dev/api/clado/contacts-enrich` | $0.20 | Get emails/phones from LinkedIn URL |

### Serper — News & Shopping ($0.04)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/serper/news` | $0.04 | Google News search |
| `POST https://stableenrich.dev/api/serper/shopping` | $0.04 | Google Shopping results |

### Reddit ($0.02)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/reddit/search` | $0.02 | Search Reddit posts |
| `POST https://stableenrich.dev/api/reddit/post-comments` | $0.02 | Get comments on a post |

### WhitePages — People & Property ($0.44)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/whitepages/person-search` | $0.44 | Lookup person by name/location |
| `POST https://stableenrich.dev/api/whitepages/property-search` | $0.44 | Lookup property records |

### Hunter — Email Verification ($0.03)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/hunter/email-verifier` | $0.03 | Verify if email is deliverable |

### Influencer Enrichment ($0.40)

| Endpoint | Price | Use for |
|---|---|---|
| `POST https://stableenrich.dev/api/influencer/enrich-by-email` | $0.40 | Find social profiles from email |
| `POST https://stableenrich.dev/api/influencer/enrich-by-social` | $0.40 | Enrich from social handle |

## Research Playbook (Fan-out Tasks)

For broad "find many candidates" requests:

1. **Seed** — Use multiple sources to build initial pool (Exa/Firecrawl for web lists, Grok for social, Google Maps for local, Apollo org-search for company IDs)
2. **Expand** — Generate query variants (synonyms, titles, regions), paginate to 50-100 candidates
3. **Enrich** — Use Apollo people-enrich + Clado to fill details, dedupe by name + company + profile URL
4. **Filter** — Apply must-haves, keep 30-50 high-confidence matches

## Tips

- Run `npx agentcash check <url>` on any endpoint to see full request/response schemas before calling
- Firecrawl for single-page scraping, Exa contents for bulk (5x cheaper)
- Apollo people-search names are obfuscated — always follow with people-enrich
- All prices in USDC on Base. Failed requests cost nothing.
