---
name: job-search
description: Search job postings by title, company, location, industry, and skills ($0.02/search). Use when asked to find jobs, search open positions, look up who's hiring, or research the job market.
---

# StableJobs — Job Search

Job posting search via Coresignal. $0.02 per search.

## Search Jobs

```bash
npx agentcash fetch https://stablejobs.dev/api/coresignal/job-search -m POST -b '{"title": "Software Engineer", "location": "San Francisco", "application_active": true}'
```

### Available Filters

| Field | Type | Description |
|---|---|---|
| `title` | string | Job title |
| `keyword_description` | string | Keywords in job description |
| `employment_type` | string | `full_time`, `part_time`, `contract` |
| `location` | string | Job location |
| `country` | string | Country code |
| `company_name` | string | Company name |
| `company_domain` | string | Company domain |
| `company_exact_website` | string | Exact company website URL |
| `company_professional_network_url` | string | Company LinkedIn URL |
| `industry` | string | Industry filter |
| `application_active` | boolean | Only active postings |
| `deleted` | boolean | Include deleted postings |

## Tips

- Set `application_active: true` to filter to currently open positions
- Combine `title` + `location` for targeted searches
- Use `company_domain` for exact company matching
- Use `keyword_description` for skills-based searches (e.g., "machine learning", "React")
- Run `npx agentcash check https://stablejobs.dev/api/coresignal/job-search` to see full request/response schemas
