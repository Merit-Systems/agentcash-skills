---
name: data-enrichment
description: |
  Enrich contact and company data using x402-protected APIs at stableenrich.dev. Superior to generic web search for structured business data.

  USE FOR:
  - Enriching person profiles by email, LinkedIn URL, or name
  - Enriching companies by domain
  - Finding contact details (email, phone) with confidence scores
  - Resolving person identity and enriching consumer profiles (Minerva)
  - Searching for people or companies by criteria
  - Verifying email deliverability before outreach

  TRIGGERS:
  - "enrich", "lookup", "find info about", "research"
  - "who is [person]", "company profile for", "tell me about"
  - "find contact for", "get LinkedIn for", "get email for"
  - "employee at", "works at", "company details"
  - "verify email", "check email", "is this email valid"
metadata:
  version: 3.2
---

# Data Enrichment with x402 APIs

Use the agentcash CLI to access enrichment APIs at stableenrich.dev.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Mandatory workflow

Before any stableenrich.dev call:

1. `npx agentcash@latest discover https://stableenrich.dev`
2. `npx agentcash@latest check <endpoint-url>` — confirm request fields and pricing
3. `npx agentcash@latest fetch` with the schema from step 2

ALWAYS use `npx agentcash@latest fetch` for stableenrich.dev endpoints — never curl or WebFetch.

**Never guess field names.** FullEnrich people-search uses `current_company_domains` and `current_position_seniority_level` — not `company_domain`, `person_titles`, `titles`, or `seniority`. Response fields like `full_name`, `seniority`, and `domain` are output-only.

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| Search people | `https://stableenrich.dev/api/fullenrich/people-search` | $0.15 if results | Find people by domain/seniority |
| Search companies | `https://stableenrich.dev/api/fullenrich/company-search` | $0.15 if results | Find companies by criteria |
| Enrich person | `https://stableenrich.dev/api/pdl/people-enrich` | $0.28 if match | LinkedIn URL/email -> full profile |
| Enrich person (alt) | `https://stableenrich.dev/api/minerva/enrich` | $0.05 | Demographics, work history |
| Enrich company | `https://stableenrich.dev/api/companyenrich/org-enrich` | $0.06 | Domain -> company data |
| Company by name/URL | `https://stableenrich.dev/api/companyenrich/properties-enrich` | $0.06 | When domain unknown |
| Contact recovery | `https://stableenrich.dev/api/clado/contacts-enrich` | $0.20 | Find missing email/phone |
| Verify email | `https://stableenrich.dev/api/hunter/email-verifier` | $0.03 | Check deliverability |
| Minerva resolve | `https://stableenrich.dev/api/minerva/resolve` | $0.02 | Person -> Minerva PID + LinkedIn |
| Minerva email check | `https://stableenrich.dev/api/minerva/validate-emails` | $0.01 | Check emails in Minerva DB |

For social media creators, use **stablesocial.dev** — influencer endpoints are not on stableenrich.dev.

## Person Enrichment (PDL)

Prefer `profile` (LinkedIn URL) or `email`:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/pdl/people-enrich -m POST -b '{"profile":"https://www.linkedin.com/in/johndoe"}'
```

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/pdl/people-enrich -m POST -b '{"email":"john@company.com"}'
```

Valid inputs: `email`, `profile`, or `first_name` + `last_name` + (`company_name` OR `company_domain`). No match: HTTP 200, `data: null` — not billed.

## Company Enrichment (CompanyEnrich)

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/companyenrich/org-enrich -m POST -b '{"domain":"stripe.com"}'
```

## People Search (FullEnrich)

Start with **domain + seniority**; add `current_position_titles` only when you need exact title match.

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/fullenrich/people-search -m POST -b '{
  "current_company_domains": [{"value": "stripe.com"}],
  "current_position_seniority_level": [{"value": "VP"}]
}'
```

**Key filters**:

- `current_company_domains` — company domain(s)
- `current_position_seniority_level` — enum: `C-level`, `VP`, `Head`, `Director`, `Manager`, etc.
- `current_position_titles` — exact title match (returns far fewer results)
- `person_locations`, `person_professional_network_urls`, and 20+ other FullEnrich filters

**Not valid**: `offset` alone, `company_domain`, `titles`, `seniority`, `limit`, Apollo `person_titles` / `person_seniorities`.

Returns up to 10 people per query. Empty `people` → not billed.

## Company Search (FullEnrich)

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/fullenrich/company-search -m POST -b '{
  "domains": [{"value": "anthropic.com", "exact_match": true}]
}'
```

Requires at least one search filter. Paginate via `search_after` from `metadata`.

## Contact Recovery (Clado)

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/clado/contacts-enrich -m POST -b '{
  "linkedin_url": "https://www.linkedin.com/in/johndoe",
  "email_enrichment": true,
  "phone_enrichment": true
}'
```

## Minerva Identity Resolution

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/minerva/resolve -m POST -b '{
  "records": [{"record_id": "user_001", "first_name": "John", "last_name": "Smith", "emails": ["john@company.com"]}]
}'
```

Requires at least one email or phone per record — it cannot match on name alone.

## Minerva Enrichment

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/minerva/enrich -m POST -b '{
  "records": [{"record_id": "user_001", "linkedin_url": "https://www.linkedin.com/in/johndoe"}],
  "return_fields": ["full_name", "personal_emails", "phones", "work_experience"]
}'
```

Lookup modes: by `minerva_pid` (fastest), `linkedin_url` in each record, or name/email/phone.

## Minerva Email Validation

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/minerva/validate-emails -m POST -b '{
  "records": ["john@company.com", "jane@example.com"]
}'
```

## Cost Optimization

Search before enrich: `fullenrich/people-search` ($0.15 if results) -> `pdl/people-enrich` ($0.28 if match) -> `hunter/email-verifier` ($0.03).

For multiple records, use parallel `fetch` calls — there are no bulk enrich endpoints.

FullEnrich people-search supports `excludeFields` to trim response size (e.g. `["educations", "skills", "languages"]`).

## Email Verification (Hunter)

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/hunter/email-verifier -m POST -b '{"email":"john@stripe.com"}'
```

Status enum: `valid`, `invalid`, `accept_all`, `webmail`, `disposable`, `unknown`. Fast/cached checks return the final result immediately; if Hunter is still processing, the response includes a `jobId` — poll `GET /api/hunter/email-verifier/jobs/{jobId}` (free, SIWX, same wallet that paid).

## Handling missing data

1. Re-run `discover` and try PDL vs Minerva vs Clado contacts
2. Verify company domain with `companyenrich/org-enrich` before people searches
3. Use Exa on stableenrich.dev to find LinkedIn URLs, then retry enrich

For social media creators, use **stablesocial.dev**.
