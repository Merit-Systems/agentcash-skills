---
name: media-generation
description: |
  Generate images and videos using x402-protected AI models at StableStudio.

  USE FOR:
  - Generating images from text prompts
  - Generating videos from text or images
  - Editing images with AI
  - Creating visual content

  TRIGGERS:
  - "generate image", "create image", "make a picture"
  - "generate video", "create video", "make a video"
  - "edit image", "modify image"
  - "stablestudio", "nano-banana", "sora", "veo", "grok", "flux", "seedance"

  ALWAYS use agentcash.fetch for stablestudio.dev endpoints.
mcp:
  - agentcash
metadata:
  version: 3
---

# Media Generation with StableStudio

Generate images and videos via x402 payments at `https://stablestudio.dev`.

## Setup

If the agentcash MCP is not yet installed, see [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

### Image Models

| Model | Endpoint | Cost | Time | Edit? |
|-------|----------|------|------|-------|
| **nano-banana-pro** (default) | `nano-banana-pro/generate` | $0.13-0.24 | ~10s | Yes |
| nano-banana | `nano-banana/generate` | $0.039 | ~5s | Yes |
| gpt-image-1.5 | `gpt-image-1.5/generate` | $0.009-0.20 | ~3s | Yes |
| gpt-image-1 | `gpt-image-1/generate` | $0.011-0.25 | ~10s | Yes |
| grok | `grok/generate` | $0.07 | ~3s | Yes |
| flux-2-pro | `flux-2-pro/generate` | $0.05-0.06 | ~5s | Yes |

All endpoints are prefixed with `https://stablestudio.dev/api/generate/`. Edit endpoints use `/edit` instead of `/generate`.

### Video Models

| Model | Endpoint | Cost | Time |
|-------|----------|------|------|
| **veo-3.1** (default) | `veo-3.1/generate` | $1.60-3.20 | 1-2min |
| veo-3.1-fast | `veo-3.1-fast/generate` | $0.40-0.80 | ~30s |
| grok-video | `grok-video/generate` | $0.15-0.75 | ~17s |
| seedance | `seedance/t2v` or `seedance/i2v` | $0.33-0.99 | ~1min |
| seedance-fast | `seedance-fast/t2v` or `seedance-fast/i2v` | $0.04-0.12 | ~40s |
| wan-2.6 | `wan-2.6/t2v` or `wan-2.6/i2v` | $0.34-1.02 | 2-5min |
| sora-2 | `sora-2/generate` | $0.40-1.20 | 1-3min |
| sora-2-pro | `sora-2-pro/generate` | $3.00-12.50 | 2-5min |

## Image Generation

**Recommended: nano-banana-pro** (best quality/cost balance)

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/nano-banana-pro/generate",
  method="POST",
  body={
    "prompt": "a cat wearing a space helmet, photorealistic",
    "aspectRatio": "16:9",
    "imageSize": "2K"
  }
)
```

**nano-banana-pro options:**
- `aspectRatio`: "1:1", "16:9", "9:16"
- `imageSize`: "1K", "2K", "4K"

### Other Image Models

**gpt-image-1.5** -- Fastest, cheapest per-image. Uses `quality` and `size` instead of aspect ratio:

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/gpt-image-1.5/generate",
  method="POST",
  body={
    "prompt": "a watercolor painting of a mountain lake",
    "quality": "high",
    "size": "1536x1024"
  }
)
```

Options: `quality`: "low"/"medium"/"high". `size`: "1024x1024", "1536x1024", "1024x1536". Same schema for gpt-image-1.

**grok** -- Fast, wide aspect ratio support. Note: uses `aspect_ratio` (underscore):

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/grok/generate",
  method="POST",
  body={
    "prompt": "neon cyberpunk cityscape at night",
    "aspect_ratio": "16:9"
  }
)
```

Options: `aspect_ratio`: "1:1", "16:9", "9:16", "4:3", "3:4", "3:2", "2:3", "2:1", "1:2", "19.5:9", "9:19.5", "20:9", "9:20".

**flux-2-pro** -- High quality with resolution control:

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/flux-2-pro/generate",
  method="POST",
  body={
    "prompt": "editorial photo of a vintage car in golden hour light",
    "aspect_ratio": "3:2",
    "resolution": "2 MP"
  }
)
```

Options: `aspect_ratio`: "1:1", "16:9", "9:16", "3:2", "2:3", "4:5", "5:4", "4:3", "3:4". `resolution`: "0.5 MP", "1 MP", "2 MP".

**nano-banana** -- Budget option ($0.039). Same schema as nano-banana-pro but without `imageSize`.

## Video Generation

**Recommended: veo-3.1** (best quality)

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/veo-3.1/generate",
  method="POST",
  body={
    "prompt": "a timelapse of clouds moving over mountains",
    "durationSeconds": "6",
    "aspectRatio": "16:9"
  }
)
```

**veo-3.1 options:**
- `durationSeconds`: "4", "6", "8"
- `aspectRatio`: "16:9", "9:16"

veo-3.1 also supports **frame interpolation** -- animate between a start image and end image by providing `image` and `lastFrame` blob URLs in the body.

**veo-3.1-fast** -- Same schema as veo-3.1, 4x cheaper, ~30s instead of 1-2min.

### Other Video Models

**seedance** -- Good quality with audio generation support. Use `seedance/t2v` (text-to-video) or `seedance/i2v` (image-to-video):

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/seedance/t2v",
  method="POST",
  body={
    "prompt": "a bird taking flight from a branch in slow motion",
    "duration": "8",
    "resolution": "1080p",
    "aspect_ratio": "16:9",
    "generate_audio": false,
    "camera_fixed": false
  }
)
```

Options: `duration`: "4"-"12". `resolution`: "480p", "720p", "1080p". `aspect_ratio`: "16:9", "9:16", "4:3", "3:4", "1:1", "21:9". For i2v, add `"image": "https://blob-url..."` and omit `generate_audio`/`camera_fixed`.

**seedance-fast** -- Same schema, 8x cheaper, slightly lower quality.

**grok-video** -- Cheapest short video option:

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/grok-video/generate",
  method="POST",
  body={
    "prompt": "a candle flame flickering",
    "duration": "6",
    "resolution": "720p",
    "aspect_ratio": "16:9"
  }
)
```

Options: `duration`: "3", "6", "9", "12", "15". `resolution`: "480p", "720p". `aspect_ratio`: "1:1", "16:9", "9:16", "4:3", "3:4", "3:2", "2:3".

**wan-2.6** -- Budget, supports t2v and i2v. `duration`: "5", "10", "15". t2v uses `size` ("1280*720", "720*1280", "1920*1080", "1080*1920"); i2v uses `resolution` ("720p", "1080p") and `image`.

**sora-2** -- `seconds`: "4", "8", "12". `size`: "1280x720", "720x1280".

**sora-2-pro** -- Premium. `seconds`: "10", "15", "25". `size`: "1280x720", "720x1280", "1792x1024", "1024x1792".

## Job Polling

Generation returns a `jobId`. Poll until complete:

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/jobs/{jobId}"
)
```

Poll images every 3s, videos every 10s. Result contains `imageUrl` or `videoUrl`.
`agentcash.fetch` handles both paid submission requests and free SIWX polling requests.

## Image Editing

Requires uploading the source image first. See [rules/uploads.md](rules/uploads.md).

All image models support `/edit`. The edit endpoint accepts `images` (array of blob URLs) plus a `prompt`.

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/nano-banana-pro/edit",
  method="POST",
  body={
    "prompt": "change the background to a beach sunset",
    "images": ["https://...blob-url..."]
  }
)
```

Grok edit also supports `aspect_ratio`. GPT-image edit uses the same `quality`/`size` options as generate.

## Model Comparison

### Image Models

| Model | Cost | Speed | Best For |
|-------|------|-------|----------|
| nano-banana-pro | $0.13-0.24 | ~10s | General purpose, up to 4K resolution |
| nano-banana | $0.039 | ~5s | Quick drafts, budget |
| gpt-image-1.5 | $0.009-0.20 | ~3s | Fastest, cheapest (quality varies) |
| gpt-image-1 | $0.011-0.25 | ~10s | Higher quality GPT images |
| grok | $0.07 | ~3s | Fast, many aspect ratios |
| flux-2-pro | $0.05-0.06 | ~5s | High quality, resolution control |

### Video Models

| Model | Cost | Speed | Best For |
|-------|------|-------|----------|
| veo-3.1 | $1.60-3.20 | 1-2min | Best quality, interpolation |
| veo-3.1-fast | $0.40-0.80 | ~30s | Quick high-quality video |
| grok-video | $0.15-0.75 | ~17s | Cheapest short videos |
| seedance | $0.33-0.99 | ~1min | Good quality, audio support |
| seedance-fast | $0.04-0.12 | ~40s | Budget video with decent quality |
| wan-2.6 | $0.34-1.02 | 2-5min | Budget, text or image input |
| sora-2 | $0.40-1.20 | 1-3min | Premium quality |
| sora-2-pro | $3.00-12.50 | 2-5min | Highest quality, longest duration |
