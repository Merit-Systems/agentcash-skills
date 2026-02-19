---
name: image-video-gen
description: Generate images and videos with AI — GPT Image 1/1.5, Flux 2 Pro, Grok, Nano Banana/Pro, Sora 2/Pro, Veo 3.1, Seedance, and Wan 2.6. Use when asked to generate an image, create AI art, make a video, edit an image, or create any visual content. Text-to-image, text-to-video, and image-to-video supported.
---

# StableStudio — AI Image & Video Generation

Pay-per-generation AI image and video. No subscriptions.

## Recommended Defaults

- **Image:** `nano-banana-pro` — best quality/cost, supports up to 4K
- **Video:** `veo-3.1` — best quality/cost, supports 1080p

## Workflow

### Image Generation

```bash
npx agentcash fetch https://stablestudio.dev/api/x402/nano-banana-pro/generate -m POST -b '{"prompt": "a mountain landscape at sunset", "aspectRatio": "16:9"}'
```

Returns `{"jobId": "...", "status": "pending"}`. Poll the job:

```bash
npx agentcash fetch https://stablestudio.dev/api/x402/jobs/JOB_ID
```

Poll every 3s until `{"status": "complete", "result": {"imageUrl": "..."}}`.

### Image Editing (requires upload)

1. Upload image via `POST https://stablestudio.dev/api/x402/uploads` ($0.01) — returns `uploadId` + `clientToken`
2. Upload file to Vercel Blob using the clientToken
3. Confirm upload via `POST https://stablestudio.dev/api/x402/uploads/confirm` (SIWX, free)
4. Use the `blobUrl` in edit request:

```bash
npx agentcash fetch https://stablestudio.dev/api/x402/nano-banana-pro/edit -m POST -b '{"prompt": "remove the background", "images": ["https://blob-url..."]}'
```

### Video Generation

Same flow as images but poll every 5s (videos take 30s-5min).

Returns `{"status": "complete", "result": {"videoUrl": "...", "thumbnailUrl": "..."}}`.

```bash
npx agentcash fetch https://stablestudio.dev/api/x402/veo-3.1/generate -m POST -b '{"prompt": "a drone shot over mountains", "durationSeconds": "8", "aspectRatio": "16:9"}'
```

## Models & Pricing

### Image Models

| Model | Generate | Edit | Speed |
|---|---|---|---|
| `nano-banana` | $0.039 | $0.039 | ~5s |
| `nano-banana-pro` | $0.13-0.24 | $0.13-0.24 | ~10s |
| `gpt-image-1` | $0.011-0.25 | $0.011-0.25 | ~10s |
| `gpt-image-1.5` | $0.009-0.20 | $0.009-0.20 | ~3s |
| `flux-2-pro` | $0.05-0.06 | $0.05-0.06 | ~5s |
| `grok` | $0.07 | $0.022 | ~3s |

### Video Models

| Model | Cost | Speed | Max |
|---|---|---|---|
| `grok-video` | $0.15-0.75 | ~17s | 15s video |
| `seedance` | $0.33-0.99 | ~1min | 12s video |
| `seedance-fast` | $0.04-0.12 | ~40s | 12s video |
| `wan-2.6` | $0.34-1.02 | 2-5min | 15s video |
| `sora-2` | $0.40-1.20 | 1-3min | 12s video |
| `sora-2-pro` | $3.00-12.50 | 2-5min | 25s video |
| `veo-3.1` | $1.60-3.20 | 1-2min | 8s video |
| `veo-3.1-fast` | $0.40-0.80 | ~30s | 8s video |

## Input Schemas

**Standard image:** `{"prompt": "...", "aspectRatio": "1:1|16:9|9:16"}`

**nano-banana-pro:** adds `"imageSize": "1K|2K|4K"`

**gpt-image:** adds `"quality": "low|medium|high"`, `"size": "1024x1024|1536x1024|1024x1536"`

**grok:** 13 aspect ratios including `"19.5:9"`, `"20:9"`, ultra-wide

**flux-2-pro:** adds `"resolution": "0.5 MP|1 MP|2 MP"`

**Video (seedance):** `{"prompt": "...", "duration": "4-12", "resolution": "480p|720p|1080p", "aspect_ratio": "16:9|9:16|1:1"}`

**Video (wan-2.6):** `{"prompt": "...", "duration": "5|10|15", "size": "1280*720|720*1280|1920*1080"}`

**Video (sora-2):** `{"prompt": "...", "seconds": "4|8|12", "size": "1280x720|720x1280"}`

**Video (veo-3.1):** `{"prompt": "...", "durationSeconds": "4|6|8", "aspectRatio": "16:9|9:16"}`

**Image-to-video:** Add `"image": "https://blob-url..."` to any video model body

**Edit:** `{"prompt": "...", "images": ["https://blob-url..."]}` (nano-banana-pro supports up to 14 images)

## Job Management (all free, no payment)

```bash
npx agentcash fetch https://stablestudio.dev/api/x402/jobs/JOB_ID                     # Check job status
npx agentcash fetch https://stablestudio.dev/api/x402/jobs?limit=20&status=complete    # List jobs
npx agentcash fetch https://stablestudio.dev/api/x402/jobs/JOB_ID -m DELETE            # Delete failed job
```

## Tips

- Run `npx agentcash check https://stablestudio.dev/api/x402/nano-banana-pro/generate` to see exact pricing (prices vary by resolution/quality)
- Poll images every 3s (2min timeout), videos every 5s (10min timeout)
- For editing, the upload flow is: pay $0.01 → upload to blob → confirm → use blobUrl
- `gpt-image-1.5` is the cheapest image model ($0.009); `seedance-fast` cheapest video ($0.04)
