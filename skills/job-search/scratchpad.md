# stablejobs skill — Scratchpad

## What works well
- Simple single-endpoint skill, easy to understand
- Filter table covers all available parameters
- Tips section has practical guidance

## Potential improvements
- Only one endpoint currently — skill may be too thin to justify standalone
- Could merge into agentcash skill or stableenrich skill since it's job "enrichment"
- Missing: response format examples (what does a job posting look like?)
- Could add pagination info if the API supports it
- Missing: result count limits
- Could add integration with Apollo (find company → search their jobs)
- Coresignal data freshness is unclear (real-time vs. cached?)
- The check_endpoint_schema showed empty paymentOptions — may need verification

## Optimization ideas
- Could combine with StableEnrich as a "research" super-skill
- Job market analysis workflow: search jobs by company → aggregate by title/location
- Integration with StablePhone: find job posting → call company about position
