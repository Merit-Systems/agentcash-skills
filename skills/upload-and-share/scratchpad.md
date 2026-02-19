# stableupload skill — Scratchpad

## What works well
- Simple 3-step flow is easy to follow
- Tier table gives clear size/price breakdown
- Tips section covers key gotchas (upload URL expiry, any file type)

## Potential improvements
- The upload step requires a raw curl PUT — Claude may struggle with binary file uploads
- Could add a script that handles the full buy-slot → upload → return-URL flow
- Missing: how to determine file size before choosing tier
- Could add integration guidance with StableEmail (upload image → use in email HTML)
- Could add integration with StableStudio (download generated image → re-upload for sharing)
- The SIWX endpoints (list/download) aren't well documented for body format
- Missing: what happens if you upload a file larger than the tier allows
- Could add Content-Type guidance (when does it matter vs. advisory?)

## Optimization ideas
- Auto-tier selection based on file size
- Batch upload script for multiple files
- Integration examples with other Stable* services
