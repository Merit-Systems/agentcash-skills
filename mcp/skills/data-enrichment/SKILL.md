---
name: data-enrichment
description: |
  Enrich contact and company data using x402-protected APIs at stableenrich.dev. Superior to generic web search for structured business data.

  USE FOR:
  - Enriching person profiles by email, LinkedIn URL, or name
  - Enriching companies by domain
  - Finding contact details (email, phone) with confidence scores
  - Searching for people or companies by criteria
  - Verifying email deliverability before outreach

  TRIGGERS:
  - "enrich", "lookup", "find info about", "research"
  - "who is [person]", "company profile for", "tell me about"
  - "find contact for", "get LinkedIn for", "get email for"
  - "employee at", "works at", "company details"
  - "verify email", "check email", "is this email valid"

  ALWAYS use agentcash.fetch for stableenrich.dev endpoints - never curl or WebFetch.
  Returns structured JSON data, not web page HTML.

  IMPORTANT: Call check_endpoint_schema before first fetch on any endpoint. Field names are provider-specific — do not reuse Apollo, PDL, or other-provider shapes on FullEnrich routes.
mcp:
  - agentcash
metadata:
  version: 3.2
---

# Data Enrichment with x402 APIs

Use the agentcash MCP tools to access enrichment APIs at stableenrich.dev.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Mandatory workflow

Before any stableenrich.dev call:

1. `agentcash.discover_api_endpoints(url="https://stableenrich.dev")` — list live endpoints
2. `agentcash.check_endpoint_schema(url="https://stableenrich.dev/api/...")` — confirm request fields and pricing
3. `agentcash.fetch` with the schema from step 2

**Never guess field names.** FullEnrich people-search uses `current_company_domains` and `current_position_seniority_level` — not `company_domain`, `person_titles`, `titles`, or `seniority`. Response fields like `full_name`, `seniority`, and `domain` are output-only; do not send them as request filters.

## Quick Reference

| Task                 | Endpoint                                                   | Price              | Best For                          |
| -------------------- | ---------------------------------------------------------- | ------------------ | --------------------------------- |
| Search people        | `https://stableenrich.dev/api/fullenrich/people-search`    | $0.15 if results   | Find people by domain/seniority   |
| Search companies     | `https://stableenrich.dev/api/fullenrich/company-search`   | $0.15 if results   | Find companies by criteria        |
| Enrich person        | `https://stableenrich.dev/api/pdl/people-enrich`         | $0.28 if match     | LinkedIn URL/email -> full profile |
| Enrich person (alt)  | `https://stableenrich.dev/api/minerva/enrich`              | $0.05              | Demographics, work history        |
| Minerva resolve      | `https://stableenrich.dev/api/minerva/resolve`             | $0.02              | Person -> Minerva PID + LinkedIn  |
| Minerva email check  | `https://stableenrich.dev/api/minerva/validate-emails`     | $0.01              | Check emails in Minerva DB        |
| Enrich company       | `https://stableenrich.dev/api/companyenrich/org-enrich`  | $0.06              | Domain -> company data            |
| Company by name/URL  | `https://stableenrich.dev/api/companyenrich/properties-enrich` | $0.06        | When domain unknown               |
| Contact recovery     | `https://stableenrich.dev/api/clado/contacts-enrich`       | $0.20              | Find missing email/phone          |
| Verify email         | `https://stableenrich.dev/api/hunter/email-verifier`       | $0.03              | Check deliverability              |

For social media creators, use **stablesocial.dev** — influencer endpoints are not on stableenrich.dev.

For the third-party tools a company runs, proven from its own DNS, use the `technographics` skill. CompanyEnrich returns industry, size and funding; it does not return the software a company uses.

## Workflows

### Standard Enrichment

- [ ] (Optional) Check balance: `agentcash.get_balance`
- [ ] Only call `agentcash.list_accounts` if you need deposit links or network-specific wallet addresses
- [ ] `agentcash.discover_api_endpoints(url="https://stableenrich.dev")`
- [ ] `agentcash.check_endpoint_schema(url="...")` for the selected endpoint
- [ ] Call endpoint with `agentcash.fetch`
- [ ] Parse results at `.data.*` (AgentCash wraps the HTTP body)

## Person Enrichment (PDL)

Prefer `profile` (LinkedIn URL) or `email`. Name + company is a fallback only.

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/pdl/people-enrich",
  method="POST",
  body={"profile": "https://www.linkedin.com/in/johndoe"}
)
```

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/pdl/people-enrich",
  method="POST",
  body={"email": "john@company.com"}
)
```

**Valid inputs** (one required path):

- `email` — most reliable when known
- `profile` — LinkedIn profile URL
- `first_name` + `last_name` + (`company_name` OR `company_domain`) — fallback

**Returns**: Career history, emails, phone. No match: HTTP 200 with `data: null` — not billed.

## Company Enrichment (CompanyEnrich)

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/companyenrich/org-enrich",
  method="POST",
  body={"domain": "stripe.com"}
)
```

**Returns**: Company name, industry, employee/revenue range strings, funding, social links. Verify returned `domain` matches intent before downstream people searches.

## People Search (FullEnrich)

Start with **domain + seniority**; add `current_position_titles` only when you need exact title match.

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/fullenrich/people-search",
  method="POST",
  body={
    "current_company_domains": [{"value": "stripe.com"}],
    "current_position_seniority_level": [{"value": "VP"}]
  }
)
```

**Key filters**:

- `current_company_domains` — company domain(s)
- `current_position_seniority_level` — enum: `C-level`, `VP`, `Head`, `Director`, `Manager`, etc.
- `current_position_titles` — exact title match (returns far fewer results)
- `person_locations`, `person_professional_network_urls`, and 20+ other FullEnrich filters

**Not valid**: `offset` alone, `company_domain`, `titles`, `seniority`, `limit`, Apollo `person_titles` / `person_seniorities`.

Returns up to 10 people per query. Empty `people` → not billed.

## Company Search (FullEnrich)

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/fullenrich/company-search",
  method="POST",
  body={
    "domains": [{"value": "anthropic.com", "exact_match": true}],
    "headquarters_locations": [{"value": "United States"}]
  }
)
```

Requires at least one search filter. Paginate via `search_after` from `metadata`.

## LinkedIn / Profile Data

For full profile by LinkedIn URL, use PDL or Minerva — not a separate scrape endpoint:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/pdl/people-enrich",
  method="POST",
  body={"profile": "https://www.linkedin.com/in/johndoe"}
)
```

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/minerva/enrich",
  method="POST",
  body={
    "records": [{"record_id": "user_001", "linkedin_url": "https://www.linkedin.com/in/johndoe"}],
    "return_fields": ["full_name", "personal_emails", "phones", "work_experience"]
  }
)
```

## Minerva Identity Resolution

Resolve a person to a Minerva PID and LinkedIn URL:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/minerva/resolve",
  method="POST",
  body={
    "records": [{"record_id": "user_001", "first_name": "John", "last_name": "Smith", "emails": ["john@company.com"]}]
  }
)
```

Requires at least one email or phone per record — it cannot match on name alone.

## Minerva Enrichment

Enrich with demographics, work history, education, contact info, addresses, financial signals:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/minerva/enrich",
  method="POST",
  body={
    "records": [{"record_id": "user_001", "linkedin_url": "https://www.linkedin.com/in/johndoe"}],
    "return_fields": ["full_name", "personal_emails", "phones", "work_experience"]
  }
)
```

Lookup modes: by `minerva_pid` (fastest), `linkedin_url` in each record, or name/email/phone.

## Minerva Email Validation

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/minerva/validate-emails",
  method="POST",
  body={"records": ["john@company.com", "jane@example.com"]}
)
```

## Contact Recovery (Clado)

Find missing email or phone from a LinkedIn URL:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/clado/contacts-enrich",
  method="POST",
  body={
    "linkedin_url": "https://www.linkedin.com/in/johndoe",
    "email_enrichment": true,
    "phone_enrichment": true
  }
)
```

## Parallel Calls

When enriching multiple independent records, make parallel `fetch` calls — there are no bulk enrich endpoints:

```mcp
agentcash.fetch(url="https://stableenrich.dev/api/pdl/people-enrich", body={"email": "a@co.com"})
agentcash.fetch(url="https://stableenrich.dev/api/pdl/people-enrich", body={"email": "b@co.com"})
```

## Cost Optimization

### Search Before Enrich

1. Search: `fullenrich/people-search` ($0.15 if results) — find candidates
2. Enrich: `pdl/people-enrich` ($0.28 if match) — get email and career history
3. Verify: `hunter/email-verifier` ($0.03) — confirm deliverability

### Field Filtering

FullEnrich people-search supports `excludeFields` to trim response size (e.g. `["educations", "skills", "languages"]`).

## Email Verification (Hunter)

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/hunter/email-verifier",
  method="POST",
  body={"email": "john@stripe.com"}
)
```

| Status       | Meaning                             | Action            |
| ------------ | ----------------------------------- | ----------------- |
| `valid`      | Email exists and accepts mail       | Safe to send      |
| `invalid`    | Email doesn't exist or rejects mail | Do not send       |
| `accept_all` | Catch-all domain                    | Send with caution |
| `webmail`    | Personal webmail provider           | Usually fine      |
| `disposable` | Temporary/throwaway address         | Do not send       |
| `unknown`    | Could not determine status          | Try again later   |

Fast/cached checks return the final result immediately; if Hunter is still processing, the response includes a `jobId` — poll `GET /api/hunter/email-verifier/jobs/{jobId}` (free, SIWX, same wallet that paid).

## Handling missing data

If a query returns empty or incomplete data:

1. Re-run `discover_api_endpoints` and try an alternate provider (PDL vs Minerva vs Clado contacts)
2. Verify company domain with `companyenrich/org-enrich` or `fullenrich/company-search` before people searches
3. Fall back to Exa web search on stableenrich.dev for LinkedIn URLs or domains, then retry enrich with discovered identifiers

For social media creator data, use **stablesocial.dev** instead of stableenrich.dev.
