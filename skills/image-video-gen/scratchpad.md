# stablestudio skill — Scratchpad

## What works well
- Recommended defaults (nano-banana-pro for images, veo-3.1 for video) prevent decision paralysis
- Model pricing table gives quick comparison
- Input schemas cover all models concisely

## Potential improvements
- Upload flow for image editing is complex (3 steps) — could be a script
- The discovery output shows $10 for all endpoints but actual prices vary by resolution/quality — the skill clarifies this but discovery is misleading
- Could add a model selection guide: "photorealistic? → gpt-image. artistic? → nano-banana-pro. fast? → gpt-image-1.5"
- Video model comparison could include quality descriptions (not just cost/speed)
- Polling logic (3s for images, 5s for videos, different timeouts) could be a reusable pattern
- Could add a references/ file with example prompts that produce good results per model
- grok has 13 aspect ratios including ultra-wide — could note use cases (phone wallpaper, social media, etc.)
- sora-2-pro is extremely expensive ($3-$12.50) — should warn before using
- The confirm upload step requires SIWX auth — could clarify that fetch_with_auth handles this

## Missing
- Quality comparison between models (which produces best results?)
- Max resolution per model
- Whether models support negative prompts
- Batch generation support
- Output format details (PNG? JPEG? WebM? MP4?)

## Optimization ideas
- Script to handle the full upload → edit → download flow
- Could cache upload tokens to avoid re-uploading the same image
- For video, a "generate and wait" helper that handles polling internally
