# stableenrich skill — Scratchpad

## What works well
- Provider guide with price tables gives quick reference
- Research Playbook codifies the fan-out methodology
- Apollo workflow warnings prevent the most common mistake (obfuscated names)

## Potential improvements
- Could add request body examples for each provider (currently relies on discover/check)
- The full request/response schemas are very detailed — could be split into references/ files per provider
- Could add a "cheapest option for X" decision tree (e.g., "need email? → Hunter. need LinkedIn? → Clado")
- Google Maps nearby-search requires lat/lng — could note that users should get coords first
- Reddit endpoint body schema isn't documented here — relies on check_endpoint_schema
- Could add pagination guidance for Apollo (page parameter)
- WhitePages is expensive ($0.44) — should note it's best used as last resort
- Influencer enrich-by-social endpoint price wasn't in the discovery output — may need verification

## Missing from API docs
- Response format examples for each provider
- Rate limiting details (if any)
- Which fields are required vs optional in request bodies
- Error response formats

## Optimization ideas
- Most common workflow is Apollo people-search → people-enrich. Could create a script that chains these
- Could add a references/schemas.md with all request/response JSON schemas
- For research tasks, a multi-provider orchestration script could parallelize queries
