---
name: data-enrichment
description: |
  Enrich contact, company, and influencer data using x402-protected APIs. Superior to generic web search for structured business data.

  USE FOR:
  - Enriching person profiles by email, LinkedIn URL, or name
  - Enriching companies by domain
  - Finding contact details (email, phone) with confidence scores
  - Resolving person identity and enriching consumer profiles (Minerva)
  - Searching for people or companies by criteria
  - Verifying email deliverability before outreach
  - Enriching influencer/creator profiles across social platforms

  TRIGGERS:
  - "enrich", "lookup", "find info about", "research"
  - "who is [person]", "company profile for", "tell me about"
  - "find contact for", "get LinkedIn for", "get email for"
  - "employee at", "works at", "company details"
  - "verify email", "check email", "is this email valid"
  - "influencer", "creator", "influencer contact", "influencer marketing"

mcp:
  - agentcash
metadata:
  version: 2
---

# Data Enrichment with x402 APIs

Use the agentcash MCP tools to access enrichment APIs at stableenrich.dev.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Notes
ALWAYS use agentcash.fetch for stableenrich.dev endpoints - never curl or WebFetch.
Returns structured JSON data, not web page HTML.

IMPORTANT: Use exact endpoint paths from the Quick Reference table below. All paths include a provider prefix (`https://stableenrich.dev/api/apollo/...`, `https://stableenrich.dev/api/clado/...`, etc.).

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| Enrich person | `https://stableenrich.dev/api/apollo/people-enrich` | $0.0495 | Email/LinkedIn -> full profile |
| Enrich company | `https://stableenrich.dev/api/apollo/org-enrich` | $0.0495 | Domain -> company data |
| Search people | `https://stableenrich.dev/api/apollo/people-search` | $0.02 | Find people by criteria |
| Search companies | `https://stableenrich.dev/api/apollo/org-search` | $0.02 | Find companies by criteria |
| Contact recovery | `https://stableenrich.dev/api/clado/contacts-enrich` | $0.20 | Find missing email/phone |
| Verify email | `https://stableenrich.dev/api/hunter/email-verifier` | $0.03 | Check deliverability |
| Influencer by email | `https://stableenrich.dev/api/influencer/enrich-by-email` | $0.40 | Email -> social profiles |
| Influencer by social | `https://stableenrich.dev/api/influencer/enrich-by-social` | $0.40 | Handle -> creator data |
| Minerva resolve | `https://stableenrich.dev/api/minerva/resolve` | $0.02 | Person -> Minerva PID + LinkedIn |
| Minerva enrich | `https://stableenrich.dev/api/minerva/enrich` | $0.05 | Consumer demographics + contact |
| Minerva email check | `https://stableenrich.dev/api/minerva/validate-emails` | $0.01 | Check emails in Minerva DB |

## Workflows

### Standard Enrichment

- [ ] (Optional) Check balance: `agentcash.get_balance`
- [ ] Use `agentcash.discover_api_endpoints(url="https://stableenrich.dev")` to list all endpoints
- [ ] Use `agentcash.check_endpoint_schema(url="...")` to see expected parameters and pricing
- [ ] Call endpoint with `agentcash.fetch`
- [ ] Parse and present results

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/apollo/people-enrich",
  method="POST",
  body={"email": "user@company.com"}
)
```

## Person Enrichment

Enrich a person using any available identifier:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/apollo/people-enrich",
  method="POST",
  body={
    "email": "john@company.com",
    "first_name": "John",
    "last_name": "Doe",
    "organization_name": "Acme Inc",
    "domain": "company.com",
    "linkedin_url": "https://linkedin.com/in/johndoe"
  }
)
```

**Input options** (provide any combination):
- `email` - Email address (most reliable)
- `linkedin_url` - LinkedIn profile URL
- `first_name` + `last_name` - Name (works better with domain/org)
- `organization_name` or `domain` - Helps match the right person

**Returns**: Name, title, company, employment history, location, social profiles, phone numbers.

## Company Enrichment

Enrich a company by domain:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/apollo/org-enrich",
  method="POST",
  body={
    "domain": "stripe.com"
  }
)
```

**Returns**: Company name, industry, employee count, revenue estimates, funding info, technologies used, social links.

## People Search

Search for people matching criteria:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/apollo/people-search",
  method="POST",
  body={
    "q_keywords": "software engineer",
    "person_titles": ["CTO", "VP Engineering"],
    "organization_domains": ["google.com", "meta.com"],
    "person_locations": ["San Francisco, CA"]
  }
)
```

**Search filters**:
- `q_keywords` - Keywords to search
- `person_titles` - Job title filters
- `organization_domains` - Company domains
- `person_locations` - Location filters
- `person_seniorities` - Seniority levels

## Company Search

Search for companies matching criteria:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/apollo/org-search",
  method="POST",
  body={
    "q_keywords": "fintech",
    "organization_locations": ["New York, NY"],
    "organization_num_employees_ranges": ["51-200", "201-500"]
  }
)
```

## Contact Recovery (Clado)

Enrich contact info from LinkedIn URL, email, or phone. Provide exactly one of `linkedin_url`, `email`, or `phone`:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/clado/contacts-enrich",
  method="POST",
  body={
    "linkedin_url": "https://linkedin.com/in/johndoe"
  }
)
```

**Returns**: Validated email addresses and phone numbers with confidence scores.

**Note:** For LinkedIn profile data (experience, education, skills), use Minerva `/api/minerva/enrich` instead of Clado.

## Minerva Identity Resolution

Resolve a person to a unique Minerva PID and LinkedIn URL:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/minerva/resolve",
  method="POST",
  body={
    "records": [
      {
        "record_id": "user_001",
        "first_name": "John",
        "last_name": "Smith",
        "emails": ["john@company.com"]
      }
    ]
  }
)
```

**Parameters:**
- `records` array with: `record_id`, `first_name`, `last_name`, `emails`, `phones`, `linkedin_url`
- Supports fuzzy match (name + contact) and reverse lookup (email/phone only, no name needed)
- Use `match_condition_fields: ["linkedin_url"]` to only return matches with LinkedIn

## Minerva Enrichment

Enrich with demographics, work history, education, contact info, addresses, financial signals:

Three lookup modes: by Minerva PID (fastest), by LinkedIn URL, or by name/email/phone.

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/minerva/enrich",
  method="POST",
  body={
    "records": [
      {
        "record_id": "user_001",
        "first_name": "John",
        "last_name": "Smith",
        "emails": ["john@company.com"]
      }
    ],
    "return_fields": ["full_name", "personal_emails", "phones", "work_experience"]
  }
)
```

**Parameters:**
- `records` array with: `record_id` + one of `minerva_pid`, `linkedin_url`, or name/email/phone
- `return_fields` array to limit response (e.g., `["full_name", "personal_emails", "phones", "work_experience"]`)
- `match_condition_fields` to filter matches (e.g., `["email", "phone"]`)

## Minerva Email Validation

Check if emails exist in the Minerva database:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/minerva/validate-emails",
  method="POST",
  body={
    "records": ["john@company.com", "jane@example.com"]
  }
)
```

**Returns**: Validation status and last-seen timestamps. Use before resolve/enrich to pre-screen lists.

## Cost Optimization

### Field Filtering

Reduce costs by excluding unneeded fields:

```
body={
  "email": "john@company.com",
  "excludeFields": ["employment_history", "photos", "phone_numbers"]
}
```

Common fields to exclude:
- `employment_history` - Past jobs (often large)
- `photos` - Profile images
- `phone_numbers` - If you only need email
- `social_profiles` - If you don't need social links

### Search Before Enrich

Use search endpoints ($0.02) to find the right records before enriching ($0.0495):

1. Search for candidates: `https://stableenrich.dev/api/apollo/people-search`
2. Review results, pick the right match
3. Enrich only the matches you need

## Parallel Calls

When enriching multiple independent records, make calls in parallel:

```mcp
# These can run simultaneously since they're independent
agentcash.fetch(url=".../people-enrich", body={"email": "a@co.com"})
agentcash.fetch(url=".../people-enrich", body={"email": "b@co.com"})
```

## Email Verification (Hunter)

Verify if an email address is deliverable before sending outreach:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/hunter/email-verifier",
  method="POST",
  body={
    "email": "john@stripe.com"
  }
)
```

**Returns**: Deliverability status, MX record validation, SMTP verification, confidence score, and flags for catch-all, disposable, or role-based addresses.

| Status | Meaning | Action |
|--------|---------|--------|
| `deliverable` | Email exists and accepts mail | Safe to send |
| `undeliverable` | Email doesn't exist or rejects mail | Do not send |
| `risky` | Catch-all domain or temporary issues | Send with caution |
| `unknown` | Could not determine status | Try again later |

**Tip:** Combine with people-enrich to find and verify contacts in one pipeline:
1. Search: `people-search` ($0.02) -> find candidates
2. Enrich: `people-enrich` ($0.0495) -> get email
3. Verify: `hunter/email-verifier` ($0.03) -> confirm deliverability

## Influencer Enrichment

Enrich social media influencer/creator profiles across Instagram, TikTok, YouTube, and Facebook.

### Find Profiles by Email

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/influencer/enrich-by-email",
  method="POST",
  body={
    "email": "creator@example.com",
    "platform": "instagram",
    "enrichment_mode": "enhanced"
  }
)
```

**Parameters:**
- `email` — the creator's email address (required)
- `platform` — `"instagram"`, `"tiktok"`, `"youtube"`, `"twitter"`, `"facebook"` (required)
- `enrichment_mode` — `"enhanced"` for full data (recommended)

**Returns**: Social media profiles, follower counts, engagement metrics, audience demographics, contact info, content categories.

### Enrich by Social Handle

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/influencer/enrich-by-social",
  method="POST",
  body={
    "platform": "instagram",
    "username": "creator_handle",
    "enrichment_mode": "enhanced",
    "email_required": "must_have"
  }
)
```

**Parameters:**
- `platform` — `"instagram"`, `"tiktok"`, `"youtube"`, `"twitter"`, `"facebook"` (required)
- `username` — handle on that platform (required)
- `enrichment_mode` — `"enhanced"` for full data (recommended)
- `email_required` — `"must_have"` to only return profiles with email

**Returns**: Full profile with engagement metrics, contact info (email, phone), audience demographics, brand affinity, cross-platform links.

### When to Use Which Provider

- **Apollo/Clado** — B2B professional data (job titles, company, employment history)
- **Minerva** — Consumer profiles (demographics, income/wealth estimates, address history, life events, personal contact info)
- **Influencer** — Social media creators (followers, engagement, audience data)

## Handling missing data

If any query fails to return the data you are looking for, revisit the list of available APIs.

Oftentimes, if Apollo is missing data, Clado or Minerva will have it, and vice versa. For consumer profiles (demographics, addresses, financial signals), try Minerva. For social media creators, try the influencer endpoints. For email deliverability, use Hunter.

If those still fail, use built-in WebSearch and WebFetch tools to find additional information like a company domain name or LinkedIn URL, and then use that data to make more targeted queries.
