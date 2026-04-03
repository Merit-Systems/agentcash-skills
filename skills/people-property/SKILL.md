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

  Use `npx agentcash@latest fetch` for Whitepages endpoints. Both endpoints are $0.44 per call.
metadata:
  version: 2
---

# People & Property Search with Whitepages

Access people and property search through x402-protected endpoints.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

**Important:** This skill provides access to personal information. Review [rules/privacy.md](rules/privacy.md) before use.

## Quick Reference

| Task | Endpoint | Price | Description |
|------|----------|-------|-------------|
| Person search | `https://stableenrich.dev/api/whitepages/person-search` | $0.44 | Find people by name/location |
| Property search | `https://stableenrich.dev/api/whitepages/property-search` | $0.44 | Property and owner info |

## Person Search

Search for a person by name and location:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/whitepages/person-search -m POST -b '{
  "first_name": "John",
  "last_name": "Smith",
  "city": "Seattle",
  "state_code": "WA"
}'
```

IMPORTANT: The state parameter is named `state_code`, NOT `state`. Use two-letter state abbreviations (e.g., 'CA', 'NY', 'TX').

**Parameters:**
- `first_name` - First name (required)
- `last_name` - Last name (required)
- `city` - City name
- `state_code` - Two-letter state abbreviation (US)
- `zip` - ZIP code
- `street` - Street address

**Returns:**
- Full name and age range
- Current and previous addresses
- Phone numbers
- Associated people (relatives, associates)

### More Specific Search

Include more details for better matches:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/whitepages/person-search -m POST -b '{
  "first_name": "John",
  "last_name": "Smith",
  "street": "123 Main St",
  "city": "Seattle",
  "state_code": "WA",
  "zip": "98101"
}'
```

## Property Search

Search for property information:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/whitepages/property-search -m POST -b '{
  "street": "123 Main Street",
  "city": "Seattle",
  "state_code": "WA"
}'
```

IMPORTANT: The state parameter is named `state_code`, NOT `state`. Use two-letter state abbreviations (e.g., 'CA', 'NY', 'TX').

**Parameters:**
- `street` - Street address (required)
- `city` - City name
- `state_code` - Two-letter state abbreviation
- `zip` - ZIP code

**Returns:**
- Property address (standardized)
- Owner name
- Property type (single family, condo, etc.)
- Property details

## Response Data

### Person Search Fields
- `name` - Full legal name
- `ageRange` - Estimated age range
- `currentAddress` - Current residence
- `historicalAddresses` - Previous addresses
- `phoneNumbers` - Associated phone numbers
- `associatedPeople` - Relatives and associates

### Property Search Fields
- `address` - Standardized address
- `owner` - Property owner name
- `propertyType` - Type of property
- `yearBuilt` - Construction year (if available)
- `bedrooms` / `bathrooms` - Property details
- `squareFootage` - Size (if available)

## Workflows

### Verify Contact Information

- [ ] Confirm legitimate purpose (see [rules/privacy.md](rules/privacy.md))
- [ ] (Optional) Check balance: `npx agentcash@latest balance`
- [ ] Search with available details
- [ ] Verify results match expected person

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/whitepages/person-search -m POST -b '{"first_name": "Jane", "last_name": "Doe", "city": "Portland", "state_code": "OR"}'
```

### Property Research

- [ ] (Optional) Check balance: `npx agentcash@latest balance`
- [ ] Search by address
- [ ] Review owner and property details

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/whitepages/property-search -m POST -b '{"street": "456 Oak Avenue", "city": "Austin", "state_code": "TX"}'
```

### Reconnect with Someone

- [ ] Confirm legitimate purpose
- [ ] Provide as much detail as possible for accuracy
- [ ] Review results for correct match

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/whitepages/person-search -m POST -b '{"first_name": "Michael", "last_name": "Johnson", "state_code": "CA"}'
```

## Cost Considerations

At $0.44 per call, Whitepages is the most expensive endpoint in the x402 suite.

| Scenario | Cost |
|----------|------|
| Single lookup | $0.44 |
| Verify address + person | $0.88 |
| Multiple candidates | $1.32+ |

**Tips to reduce costs:**
- Provide as much info as possible for accurate first-try results
- Use free sources first (LinkedIn, company websites)
- Use apollo, clado, firecrawl, WebSearch, WebFetch, to get data that will make the queries more accurate
- Only use for essential lookups

## Limitations

- US-focused data
- Results depend on public records availability
- Some individuals may have limited information
- Recently moved individuals may show old addresses
- Unlisted/private numbers not included
