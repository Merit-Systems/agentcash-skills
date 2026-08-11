---
name: local-search
description: |
  Search for places, businesses, and locations using Google Maps APIs via x402.

  USE FOR:
  - Finding businesses by name or type
  - Searching nearby places
  - Getting detailed place information (address, hours, reviews)
  - Finding restaurants, stores, services in an area
  - Getting business ratings and reviews
  - Getting aerial flyover videos of an address
  - Getting solar/rooftop insights and imagery for a building

  TRIGGERS:
  - "find", "search for", "locate", "nearby"
  - "restaurants near", "hotels in", "stores around"
  - "business details", "opening hours", "reviews for"
  - "places in", "what's near", "directions to"
  - "aerial view", "flyover video", "solar potential", "rooftop"

  Use `npx agentcash@latest fetch` for Google Maps endpoints. Choose partial ($0.02) vs full ($0.05-0.08) based on data needs.
metadata:
  version: 2
---

# Local Search with Google Maps

Access Google Maps Places API through x402-protected endpoints.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Data Included |
|------|----------|-------|---------------|
| Text search (basic) | `https://stableenrich.dev/api/google-maps/text-search/partial` | $0.02 | Name, address, rating |
| Text search (full) | `https://stableenrich.dev/api/google-maps/text-search/full` | $0.08 | + reviews, atmosphere |
| Nearby search (basic) | `https://stableenrich.dev/api/google-maps/nearby-search/partial` | $0.02 | Name, address, rating |
| Nearby search (full) | `https://stableenrich.dev/api/google-maps/nearby-search/full` | $0.08 | + reviews, atmosphere |
| Place details (basic) | `https://stableenrich.dev/api/google-maps/place-details/partial` | $0.02 | Core info |
| Place details (full) | `https://stableenrich.dev/api/google-maps/place-details/full` | $0.05 | All fields |
| Aerial view lookup (GET) | `https://stableenrich.dev/api/google-maps/aerial-view/lookup-video` | $0.01 | Flyover video URIs + metadata |
| Aerial view render (POST) | `https://stableenrich.dev/api/google-maps/aerial-view/render-video` | $0.01 | Render new flyover video |
| Solar building insights (GET) | `https://stableenrich.dev/api/google-maps/solar/building-insights` | $0.02 | Roof/solar potential, imageryDate |
| Solar data layers (GET) | `https://stableenrich.dev/api/google-maps/solar/data-layers` | $0.08 | GeoTIFF URLs (rgb, dsm, flux) |
| Solar RGB image (GET) | `https://stableenrich.dev/api/google-maps/solar/rgb-image` | $0.05 | GeoTIFF layer rendered as PNG/JPEG |

See [rules/partial-vs-full.md](rules/partial-vs-full.md) for tier selection guidance.

## Text Search

Search for places by text query:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/google-maps/text-search/partial -m POST -b '{"textQuery": "coffee shops in downtown Seattle"}'
```

**Parameters:**
- `textQuery` - Search query (required)
- `locationBias` - Prefer results near a location (`circle` with `center` lat/lng and `radius` in meters, max 50000)
- `includedType` - Single Google Places type to filter by
- `minRating` - Minimum rating filter (0-5)
- `openNow` - Only open places
- `maxResultCount` - Results per page, 1-5 (default: 5)
- `priceLevels` - Filter by price level (e.g. `PRICE_LEVEL_INEXPENSIVE`)
- `pageToken` - Pagination cursor from a previous response

**Pagination:** Responses include `nextPageToken` when more results exist. To fetch the next page, repeat the full original body (including `textQuery`) and add `pageToken` copied verbatim. A `pageToken` is only valid for the search that produced it.

**Full tier** adds: reviews, atmosphere data, photos, price level.

## Nearby Search

Search for places near a location:

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/google-maps/nearby-search/partial -m POST -b '{
  "locationRestriction": {
    "circle": {
      "center": {"latitude": 47.6062, "longitude": -122.3321},
      "radius": 1000
    }
  },
  "includedTypes": ["restaurant", "cafe"]
}'
```

**Parameters:**
- `locationRestriction` - Circle with center (lat/lng) and radius in meters, max 50000 (required)
- `includedTypes` - Place types to include (official Google Places types only, e.g. `restaurant`; use text search for natural-language categories)
- `excludedTypes` - Place types to exclude
- `maxResultCount` - Results per page, 1-5 (default: 5)
- `rankPreference` - `POPULARITY` (default) or `DISTANCE`

## Place Details

Get detailed info for a specific place:

```bash
npx agentcash@latest fetch "https://stableenrich.dev/api/google-maps/place-details/partial?placeId=ChIJN1t_tDeuEmsRUsoyG83frY4"
```

**Input:**
- `placeId` - Google Place ID (from search results)

**Partial returns:** Name, address, phone, website, hours, rating, types.

**Full returns:** + reviews, atmosphere (wheelchair access, pets allowed), photos, price level.

## Aerial View

Get a cinematic flyover video for an address. Look up an existing video first ($0.01); request a render if none exists ($0.01).

```bash
npx agentcash@latest fetch "https://stableenrich.dev/api/google-maps/aerial-view/lookup-video?address=500%20W%202nd%20St%2C%20Austin%2C%20TX"
```

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/google-maps/aerial-view/render-video -m POST -b '{"address": "500 W 2nd St, Austin, TX"}'
```

**Lookup input:** `address` or `videoId` (one required).

**Render input:** `address` (required).

**Returns:** `state`, `videoId`, video `uris` (landscape/portrait/thumbnail), capture metadata. Rendering is async — poll lookup-video until `state` is active.

## Solar

Get rooftop solar insights and imagery for a location:

```bash
npx agentcash@latest fetch "https://stableenrich.dev/api/google-maps/solar/building-insights?latitude=37.4450&longitude=-122.1390"
```

**Building insights ($0.02):** `latitude`, `longitude` (required), `requiredQuality` (LOW/MEDIUM/HIGH, default HIGH). Returns closest building's roof/solar potential and `imageryDate`.

**Data layers ($0.08):** `latitude`, `longitude` (required), `radiusMeters` (default 50, max 175), `view`, `requiredQuality`, `pixelSizeMeters`. Returns GeoTIFF URLs (`rgbUrl`, `dsmUrl`, flux/shade/mask URLs) with `imageryDate`.

**RGB image ($0.05):** Renders a Solar GeoTIFF layer as PNG/JPEG. Pass either a GeoTIFF `id` or `latitude`/`longitude`, plus `layer` (rgb, dsm, annualFlux, monthlyFlux, hourlyShade, mask), `radiusMeters` (default 30, max 100), `month`/`hour` for flux/shade bands, `format`, `scale`, `crop`, `quality`. Returns JSON with a public image URL. monthlyFlux: one month or 4x3 grid; hourlyShade: one hour or 6x4 grid.

```bash
npx agentcash@latest fetch "https://stableenrich.dev/api/google-maps/solar/rgb-image?latitude=37.4450&longitude=-122.1390&layer=annualFlux"
```

## Common Place Types

Use these with `includedTypes` / `excludedTypes`:

**Food & Drink:** restaurant, cafe, bar, bakery, coffee_shop

**Lodging:** hotel, motel, lodging, guest_house

**Shopping:** shopping_mall, store, supermarket, clothing_store

**Services:** bank, atm, gas_station, car_repair, car_wash

**Health:** hospital, pharmacy, doctor, dentist

**Entertainment:** movie_theater, museum, park, gym

## Workflows

### Find Businesses in Area

- [ ] (Optional) Check balance: `npx agentcash@latest balance`
- [ ] Text search (partial) to find options
- [ ] Review results and select top picks
- [ ] Get full details for selected places

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/google-maps/text-search/partial -m POST -b '{"textQuery": "Italian restaurants downtown Portland"}'
```

```bash
npx agentcash@latest fetch "https://stableenrich.dev/api/google-maps/place-details/full?placeId=ChIJ..."
```

### Nearby Search with Filters

- [ ] Get coordinates for the area
- [ ] Search with location restriction and filters
- [ ] Present sorted results

```bash
npx agentcash@latest fetch https://stableenrich.dev/api/google-maps/nearby-search/partial -m POST -b '{
  "locationRestriction": {
    "circle": {
      "center": {"latitude": 40.7128, "longitude": -74.0060},
      "radius": 500
    }
  },
  "includedTypes": ["restaurant"],
  "rankPreference": "DISTANCE"
}'
```

### Compare Places with Reviews

- [ ] Search to get place IDs
- [ ] Fetch full details for each candidate
- [ ] Compare ratings, reviews, and amenities

```bash
npx agentcash@latest fetch "https://stableenrich.dev/api/google-maps/place-details/full?placeId=place_id_here"
```

## Cost Optimization

### Use Partial Tier When:
- Just need name, address, basic info
- Browsing/discovering options
- Initial search before drilling down

### Use Full Tier When:
- Need reviews/ratings details
- Need atmosphere info (accessibility, etc.)
- Final decision-making

### Efficient Patterns

1. **Search partial, detail full:**
   - Text search (partial) to find places
   - Place details (full) for the one you care about

2. **Batch searches:**
   - Combine filters to reduce calls
   - Use `maxResultCount` to limit results

3. **Cache place IDs:**
   - Place IDs are stable
   - Re-fetch details only when needed

## Response Data

### Partial Tier Fields
- `name` - Place name
- `formattedAddress` - Full address
- `location` - Lat/lng coordinates
- `rating` - Average rating (1-5)
- `userRatingCount` - Number of ratings
- `types` - Place type categories
- `businessStatus` - OPERATIONAL, CLOSED, etc.
- `regularOpeningHours` - Hours of operation
- `nationalPhoneNumber` - Phone number
- `websiteUri` - Website URL

### Full Tier Additional Fields
- `reviews` - User reviews with text and ratings
- `priceLevel` - $ to $$$$
- `accessibilityOptions` - Wheelchair accessible, etc.
- `parkingOptions` - Parking availability
- `paymentOptions` - Accepted payment methods
- `photos` - Photo references
