---
name: enrich-knowledge-graph
description: |
  Enrich people in a knowledge graph or wiki with contact and social media information — LinkedIn, email, phone, Twitter/X — using premium enrichment APIs via the agentcash CLI. Use this skill when the user wants to enrich contacts, find someone's LinkedIn or email, fill in missing contact info for people in their wiki or knowledge base, or says 'enrich', 'find contact info', 'look up LinkedIn', 'fill in missing info', or anything about augmenting people/contact pages with external data.
argument-hint: "[all | <person-name>]"
metadata:
  version: 1
---

# Enrich Knowledge Graph

Enrich people pages in a user's knowledge graph or wiki with contact and social media data from premium APIs (Minerva, PDL) via the `agentcash` CLI. Minerva is the primary API — it returns rich data (work/personal emails, phones, Twitter, demographics, wealth signals) at $0.05/person. PDL (People Data Labs) is the fallback for name+company lookups when no LinkedIn URL is available ($0.28/match, free when no match). The APIs cost real money (typically 5-30 cents per person), so the skill guides the user through funding before making any paid calls.

## Interaction rules

Only ever ask the user **one question at a time**. Never present multiple decisions or questions simultaneously. Be conversational — guide them step by step.

## Workflow

### Step 0: Discover the knowledge graph

Before doing anything else, understand the structure of the user's knowledge graph. Don't assume any particular directory layout, frontmatter schema, or file naming convention.

1. Look at the project's CLAUDE.md, README, or any top-level documentation to understand the project structure.
2. Find where people pages live. Common patterns:
   - `wiki/people/`, `people/`, `contacts/`, `network/`
   - Markdown files with `tags: [person]` or `type: person` in frontmatter
   - A directory index or manifest that lists people
3. Read 2-3 sample people pages to learn the frontmatter schema. Look for fields that store:
   - **Name**: `title`, `name`, `first_name`/`last_name`, `full_name`, etc.
   - **Contact info**: `email`, `linkedin`, `twitter`, `x`, `phone`, `social`, etc.
   - **Context**: `org`, `company`, `role`, `title`, `location`, etc.
4. Note the schema you discover — you'll need it when writing enrichment data back.

If you can't find people pages, ask the user: "Where do your people pages live?"

### Step 1: Check balance

Run:

```bash
npx agentcash balance
```

If the balance is zero or very low (under $0.50):

1. Explain: "Enrichment uses premium APIs. It typically costs 5 cents per person with Minerva Enrich (primary), or 28 cents with PDL (fallback for name+company lookups). To enrich 20 people you'd need about $1-2."
2. Ask: "Would you like to sign up for free credits, or add funds directly to your wallet?"
3. If they want **free credits**:
   - Run `open https://agentcash.dev/onboard` to open signup in a browser.
   - Tell the user: "Complete the signup flow — it will give you a code. Paste it here and I'll activate your credits."
   - When they paste the code, run `npx agentcash onboard <code>` to redeem it. This is what actually deposits the credits into their wallet.
4. If they want to **add funds**: run `npx agentcash accounts` and give them the deposit URL from the output so they can send USDC.

After the user confirms they have funds, re-check balance with `npx agentcash balance` and proceed.

### Step 2: Scan knowledge graph

1. List all people pages from the directory discovered in Step 0.
2. Read each person page and parse whatever frontmatter format the project uses.
3. Extract what you can identify as: name, email, linkedin, phone, twitter/x, org, role, location.
4. Classify each person:
   - **Complete**: has at least email AND linkedin
   - **Partial**: has some contact info but missing email or linkedin
   - **Missing**: has no email, no linkedin, and no phone
5. Present a one-line summary: "Found N people. M are missing contact information."
6. Ask: "Want to enrich all M people with missing info, or pick specific ones?"

If the user passed an argument (e.g., `/enrich-knowledge-graph John Doe`), skip the scan summary and go straight to enriching that person.

### Step 3: Enrich each person

For each person to enrich, build the best possible query from their existing data and call the APIs.

#### Strategy: use what you have

The more identifying info you provide, the better the match. Prioritize fields in this order:
- LinkedIn URL (strongest signal — use Minerva Enrich directly)
- Email or phone (use Minerva Resolve to find LinkedIn, then Enrich)
- First name + last name + company/org (use PDL as fallback)
- First name + last name alone (weakest — try Minerva Resolve with email/phone if available; PDL cannot match on name alone either)

#### Primary path: Minerva Enrich (~$0.05)

Minerva returns rich data at low cost: work emails, personal emails, phone numbers, Twitter, demographics, wealth signals, and full work history. It works best when you have a LinkedIn URL.

```bash
npx agentcash fetch https://stableenrich.dev/api/minerva/enrich \
  --method POST \
  --body '{"records":[{"record_id":"1","linkedin_url":"https://linkedin.com/in/johndoe"}]}'
```

You can also pass `minerva_pid`, `first_name`/`last_name`, `emails`, or `phones` in the record. LinkedIn URL gives the best results.

From the response, extract:
- `professional_emails[0].email_address` -> work email
- `personal_emails[0].email_address` -> personal email
- `phones[0].phone_number` -> phone
- `twitter_url` -> twitter/x
- `linkedin_url` -> linkedin
- `work_experience` (where `work_status: "current"`) -> role, org
- `education_experience` -> education
- `estimated_income_range`, `estimated_wealth_range` -> financial signals (optional, don't write to wiki unless schema supports it)

#### Finding LinkedIn first: Minerva Resolve (~$0.02)

If you don't have a LinkedIn URL but have an email or phone, use Minerva Resolve to find one:

```bash
npx agentcash fetch https://stableenrich.dev/api/minerva/resolve \
  --method POST \
  --body '{"records":[{"record_id":"1","first_name":"John","last_name":"Doe","emails":["john@example.com"]}]}'
```

**Important**: Minerva Resolve requires at least one email or phone number. It cannot match on name alone. If `is_match: true`, follow up with Minerva Enrich using the returned `linkedin_url` or `minerva_pid`.

#### Fallback: PDL Person Enrich (~$0.28 per match, free when no match)

Use PDL (People Data Labs) when Minerva fails or when you only have a name + company (no LinkedIn URL, no email, no phone). PDL can match on name + organization without needing a LinkedIn URL.

```bash
npx agentcash fetch https://stableenrich.dev/api/pdl/people-enrich \
  --method POST \
  --body '{"first_name":"John","last_name":"Doe","company_name":"Acme Corp"}'
```

Requires one of: `email`, `profile` (LinkedIn URL), or `first_name` + `last_name` + (`company_name` OR `company_domain`). Name alone returns 400. Prefer `email` when known, then `profile`. The response has `status` (200 = found, 404 = no record — HTTP still 200 with `data: null`, not billed) and `data` with the person record: emails, phone, LinkedIn/Twitter URLs, title, company, location, and career history.

#### Validate matches

After each enrichment call, check whether the result looks like a genuine match — does the name, company, and role align with what you already know about this person? If the match looks wrong, skip it and note it in the report. Don't write bad data to the knowledge graph.

### Step 4: Update pages

For each successfully enriched person:

1. Re-read the current page (it may have changed).
2. Update **only the frontmatter or structured metadata** — do not touch the article body.
3. **Only fill in missing fields.** Never overwrite existing data. If the page already has an email and the API returns a different one, leave the existing one.
4. Write the enrichment data back using the **same field names and format** the project already uses (discovered in Step 0). Don't introduce new field names that are inconsistent with the existing schema.
5. Common mappings from API responses (adapt field names to match the project's schema):
   - **Minerva Enrich**:
     - `professional_emails[0].email_address` -> email field (prefer rank 1)
     - `personal_emails[0].email_address` -> personal email (if schema supports it)
     - `linkedin_url` -> linkedin field
     - `phones[0].phone_number` -> phone field
     - `twitter_url` -> twitter/x field
     - `work_experience` (first entry where `work_status: "current"`) -> role (`work_title`), org (`work_company_name`)
     - Work experience `work_city` + `work_state` -> location field
   - **PDL Person Enrich** (fallback — fields live under `data`):
     - `data.work_email` (or `data.emails`) -> email field
     - `data.linkedin_url` -> linkedin field
     - `data.mobile_phone` (or `data.phone_numbers[0]`) -> phone field
     - `data.twitter_url` -> twitter/x field (extract handle from URL if the project stores handles)
     - `data.job_title` -> role/title field
     - `data.job_company_name` -> org/company field
     - `data.location_name` -> location field
     - Field names inside `data` are provider-defined — run `npx agentcash check https://stableenrich.dev/api/pdl/people-enrich` if a mapping looks off.
6. If the project uses an `updated` date field, set it to today.

### Step 5: Report

After all enrichments, show a summary:

- Total people processed
- Successfully enriched (with breakdown: emails found, LinkedIn profiles found, phone numbers found)
- Failed to enrich (list names)
- Estimated total cost (count API calls: Minerva Enrich = $0.05 each, Minerva Resolve = $0.02, PDL Enrich = $0.28 per match)

## API reference

If you're unsure about the exact request/response schema for any endpoint, run:

```bash
npx agentcash check <url>
```

To see all available enrichment endpoints:

```bash
npx agentcash discover stableenrich.dev
```
