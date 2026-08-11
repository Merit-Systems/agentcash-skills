---
name: people-property
description: |
  Search for people and properties using Whitepages APIs via x402.

  USE FOR:
  - Finding people by name and location
  - Address lookups and verification
  - Property owner information
  - Background research (with legitimate purpose)

  TRIGGERS:
  - "find person", "lookup person", "who lives at"
  - "property owner", "property search", "address lookup"
  - "person at address", "contact info for"

  IMPORTANT: These endpoints contain personal information. Use responsibly and only for legitimate purposes.
  See rules/privacy.md for guidance.

  Use agentcash.fetch for Whitepages endpoints. Both endpoints are $0.22 per call.
mcp:
  - agentcash
metadata:
  version: 2.2
---

# People & Property Search with Whitepages

Access people and property search through x402-protected endpoints.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

**Important:** This skill provides access to personal information. Review [rules/privacy.md](rules/privacy.md) before use.

## Quick Reference

| Task | Endpoint | Price | Description |
|------|----------|-------|-------------|
| Person search | `https://stableenrich.dev/api/whitepages/person-search` | $0.22 | Find people by name/location |
| Property search | `https://stableenrich.dev/api/whitepages/property-search` | $0.22 | Property and owner info |

## Person Search

Search for a person by name and location:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/whitepages/person-search",
  method="POST",
  body={
    "first_name": "John",
    "last_name": "Smith",
    "city": "Seattle",
    "state_code": "WA"
  }
)
```

IMPORTANT: The state parameter is named `state_code`, NOT `state`. Use two-letter state abbreviations (e.g., 'CA', 'NY', 'TX').

**Parameters:**
- `first_name` - First name
- `last_name` - Last name
- `name` - Full name (alternative to first/last)
- `phone` - Phone number
- `city` - City name
- `state_code` - Two-letter state abbreviation (US)
- `zipcode` - ZIP code (NOT `zip`)
- `street` - Street address
- `min_age` / `max_age` - Age filters
- `radius` - Radius in miles from address (max 100)

**Returns:**
- Full name, aliases, and date of birth
- Current and historic addresses
- Phone numbers and emails
- Relatives, owned properties, job title/company

### More Specific Search

Include more details for better matches:

```mcp
agentcash.fetch(
  url=".../whitepages/person-search",
  body={
    "first_name": "John",
    "last_name": "Smith",
    "street": "123 Main St",
    "city": "Seattle",
    "state_code": "WA",
    "zipcode": "98101"
  }
)
```

## Property Search

Search for property information:

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/whitepages/property-search",
  method="POST",
  body={
    "street": "123 Main Street",
    "city": "Seattle",
    "state_code": "WA"
  }
)
```

IMPORTANT: The state parameter is named `state_code`, NOT `state`. Use two-letter state abbreviations (e.g., 'CA', 'NY', 'TX').

**Parameters:**
- `street` - Street address (required; aliases: `address`, `street_line`, `street_address`)
- `city` - City name
- `state_code` - Two-letter state abbreviation (NOT `state`)
- `zipcode` - ZIP code (NOT `zip` or `postal_code`)

**Returns:**
- Property address (standardized)
- Owners and residents (with phones/emails)
- Property details, market value, sale history, tax info

Note: If no record is indexed for the address, the endpoint returns 200 with `{ "result": null }`, not a 404.

## Response Data

### Person Search Fields

Response is `{ "persons": [...] }`. Each person includes:
- `name` / `aliases` - Full name and known aliases
- `date_of_birth` - Date of birth (if available)
- `current_addresses` / `historic_addresses` - Current and previous addresses
- `phones` - Phone numbers with type and score
- `emails` - Associated email addresses
- `relatives` - Related people
- `owned_properties` - Properties owned
- `linkedin_url` / `company_name` / `job_title` - Professional info (if available)

### Property Search Fields

Response is `{ "result": {...} }` (or `{ "result": null }` when no record):
- `property_address` / `mailing_address` - Standardized addresses
- `ownership_info` - `owner_type`, `person_owners`, `business_owners`
- `residents` - Current residents with phones/emails
- `property_details` - `year_built`, `bedrooms`, `bathrooms`, `building_area`, `lot_size`, `property_use`, etc.
- `market_value` - Estimated value and range
- `sale_history` - Past sales with dates, prices, buyer/seller
- `tax_info` - Assessed value and tax amounts

## Workflows

### Verify Contact Information

- [ ] Confirm legitimate purpose (see [rules/privacy.md](rules/privacy.md))
- [ ] (Optional) Check balance: `agentcash.get_balance`
- [ ] Search with available details
- [ ] Verify results match expected person

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/whitepages/person-search",
  method="POST",
  body={"first_name": "Jane", "last_name": "Doe", "city": "Portland", "state_code": "OR"}
)
```

### Property Research

- [ ] (Optional) Check balance: `agentcash.get_balance`
- [ ] Search by address
- [ ] Review owner and property details

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/whitepages/property-search",
  method="POST",
  body={"street": "456 Oak Avenue", "city": "Austin", "state_code": "TX"}
)
```

### Reconnect with Someone

- [ ] Confirm legitimate purpose
- [ ] Provide as much detail as possible for accuracy
- [ ] Review results for correct match

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/whitepages/person-search",
  method="POST",
  body={"first_name": "Michael", "last_name": "Johnson", "state_code": "CA"}
)
```

## Cost Considerations

At $0.22 per call, Whitepages is among the more expensive endpoints in the x402 suite.

| Scenario | Cost |
|----------|------|
| Single lookup | $0.22 |
| Verify address + person | $0.44 |
| Multiple candidates | $0.66+ |

**Tips to reduce costs:**
- Provide as much info as possible for accurate first-try results
- Use free sources first (LinkedIn, company websites)
- Use clado, minerva, firecrawl, WebSearch, WebFetch, to get data that will make the queries more accurate
- Only use for essential lookups

## Limitations

- US-focused data
- Results depend on public records availability
- Some individuals may have limited information
- Recently moved individuals may show old addresses
- Unlisted/private numbers not included
