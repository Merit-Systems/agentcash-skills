# Partial vs Full Tier Selection

Google Maps endpoints offer two pricing tiers. Choose based on what data you need.

## Quick Decision

| Need | Tier | Price |
|------|------|-------|
| Basic discovery | Partial | $0.02 |
| Name, address, hours | Partial | $0.02 |
| Reviews and ratings text | Full | $0.05-0.08 |
| Atmosphere data | Full | $0.05-0.08 |
| Photos | Full | $0.05-0.08 |

## Partial Tier ($0.02)

**Included:**
- Place name and address
- Location coordinates
- Basic rating (number)
- User rating count
- Business status
- Opening hours
- Phone and website
- Place types

**Best for:**
- Initial search/discovery
- Building lists of options
- Basic info lookup
- When reviews don't matter

## Full Tier ($0.05-0.08)

**Additional fields:**
- Full review text and author info
- Price level ($ to $$$$)
- Accessibility options
- Parking information
- Payment methods accepted
- Photo references
- Atmosphere details

**Best for:**
- Final decision-making
- When reviews are important
- Accessibility research
- Detailed comparisons

## Cost Comparison

| Scenario | Partial Cost | Full Cost |
|----------|--------------|-----------|
| Search 20 places | $0.40 | $1.60 |
| Get 5 place details | $0.10 | $0.25 |
| Typical workflow | $0.50 | $1.85 |

## Recommended Pattern

1. **Search with partial:** Find candidates cheaply
2. **Detail with full:** Get reviews for top picks

Example:
```
# Step 1: Partial search ($0.02)
text-search/partial -> 20 results

# Step 2: Full details on top 3 ($0.15)
place-details/full -> reviews, atmosphere
```

Total: $0.17 instead of $1.60 for all full searches.

## When to Use Full for Search

Use full-tier search when:
- You need reviews for ALL results
- Comparing many places at once
- Atmosphere matters for every option

Otherwise, search partial and get full details selectively.
