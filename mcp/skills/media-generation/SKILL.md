---
name: media-generation
description: |
  Generate images, videos, and SVGs using x402-protected AI models at StableStudio.

  USE FOR:
  - Generating images from text prompts
  - Generating videos from text or images
  - Editing images with AI
  - Vectorizing images to SVG
  - Creating visual content

  TRIGGERS:
  - "generate image", "create image", "make a picture"
  - "generate video", "create video", "make a video"
  - "edit image", "modify image"
  - "vectorize", "convert to svg"
  - "stablestudio", "nano-banana", "sora", "veo", "grok", "flux", "seedance", "gpt-image"

  ALWAYS use agentcash.fetch for stablestudio.dev endpoints.
mcp:
  - agentcash
metadata:
  version: 3.1
---

# Media Generation with StableStudio

Generate images and videos via x402 payments at `https://stablestudio.dev`.

## Setup

If the agentcash MCP is not yet installed, see [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Pricing

All generation endpoints are **dynamically priced** async jobs. The static price advertises a range (up to $10; seedance up to $20) — the exact price depends on your options (quality, resolution, duration) and is quoted via x402 before payment, so agentcash shows the real cost per request. Verified sample quotes: gpt-image-2 $0.21 (defaults) / $0.01 (low quality, 1024x1024); flux-2-max $0.07 (defaults); seedance t2v $0.36 (4s, 480p).

## Quick Reference

### Image Models

| Model | Endpoint | Cost | Time | Edit? |
|-------|----------|------|------|-------|
| **gpt-image-2** (default) | `gpt-image-2/generate` | $0.01-0.21 by quality/size | can take minutes | Yes |
| gpt-image-1.5 | `gpt-image-1.5/generate` | dynamic | ~3s | Yes |
| nano-banana-pro | `nano-banana-pro/generate` | dynamic | ~10s | Yes |
| nano-banana | `nano-banana/generate` | dynamic | ~5s | Yes |
| grok | `grok/generate` | dynamic | ~3s | Yes |
| flux-2-pro | `flux-2-pro/generate` | dynamic | ~5s | Yes |
| flux-2-max | `flux-2-max/generate` | ~$0.07 at defaults | — | Yes |

All endpoints are prefixed with `https://stablestudio.dev/api/generate/`. Edit endpoints use `/edit` instead of `/generate`.

### Video Models

| Model | Endpoint | Cost | Time |
|-------|----------|------|------|
| **veo-3.1** (default) | `veo-3.1/generate` | dynamic | 1-2min |
| veo-3.1-fast | `veo-3.1-fast/generate` | dynamic | ~30s |
| grok-video | `grok-video/generate` | dynamic | ~17s |
| seedance (Seedance 2 Pro) | `seedance/t2v` or `seedance/i2v` | dynamic, up to $20 ($0.36 for 4s/480p) | ~1min |
| seedance-fast (Seedance 2 Fast) | `seedance-fast/t2v` or `seedance-fast/i2v` | dynamic, up to $20 | ~40s |
| wan-2.6 | `wan-2.6/t2v` or `wan-2.6/i2v` | dynamic | 2-5min |
| sora-2 | `sora-2/generate` | dynamic | 1-3min |
| sora-2-pro | `sora-2-pro/generate` | dynamic | 2-5min |

### SVG Vectorization

| Model | Endpoint | Cost |
|-------|----------|------|
| arrow-1.1 | `arrow-1.1/vectorize` | dynamic |
| arrow-1.1-max | `arrow-1.1-max/vectorize` | dynamic |

## Image Generation

**Default: gpt-image-2.** This is the best default image model. Note: it can take minutes — poll the jobId and do **not** resubmit pending jobs. Use it unless the user specifically requests a different model or has a strong reason (e.g., speed, budget, or ultra-wide aspect ratios from grok).

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/gpt-image-2/generate",
  method="POST",
  body={
    "prompt": "a cat wearing a space helmet, photorealistic",
    "quality": "high",
    "size": "1536x1024"
  }
)
```

**gpt-image-2 options:**
- `quality`: "low", "medium", "high" (affects cost: ~$0.01 low to ~$0.21 high)
- `size`: "1024x1024", "1536x1024", "1024x1536", "auto"
- `background`: "opaque", "auto". `output_format`: "png", "jpeg", "webp"

### Other Image Models

**gpt-image-1.5** -- Fastest GPT image model. Same schema as gpt-image-2; its `background` additionally supports "transparent":

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

Options: `quality`: "low"/"medium"/"high". `size`: "1024x1024", "1536x1024", "1024x1536", "auto".

**nano-banana-pro** -- High quality, supports up to 4K:

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

Options: `aspectRatio`: "1:1", "2:3", "3:2", "3:4", "4:3", "4:5", "5:4", "9:16", "16:9", "21:9". `imageSize`: "1K", "2K", "4K".

**nano-banana** -- Budget option. Adds extreme aspect ratios ("1:4", "4:1", "1:8", "8:1") on top of nano-banana-pro's, `imageSize` adds "512", and supports `thinkingLevel`: "minimal" (fastest) or "high" (best quality).

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

**flux-2-max** -- Same schema as flux-2-pro, higher quality tier; `resolution` additionally supports "4 MP".

## SVG Vectorization

Convert any public image URL to SVG with **arrow-1.1** (or **arrow-1.1-max** for higher quality):

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/arrow-1.1/vectorize",
  method="POST",
  body={
    "image": "https://example.com/logo.png"
  }
)
```

Optional: `target_size` (square resize target in pixels, 128-4096, default 1024), `auto_crop` (crop to dominant subject, default false). Poll the returned jobId for the SVG result.

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
- `resolution`: "720p", "1080p"
- `negativePrompt`: things to avoid

veo-3.1 also supports image inputs via `imageMode` ("none", "first-frame", "reference", "interpolation"): **frame interpolation** between `image` and `lastFrame` blob URLs, or up to 3 `referenceImages` for style guidance.

**veo-3.1-fast** -- Same schema as veo-3.1, cheaper, ~30s instead of 1-2min.

### Other Video Models

**seedance** (Seedance 2 Pro) -- High quality, up to 15s. Use `seedance/t2v` (text-to-video) or `seedance/i2v` (image-to-video):

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/seedance/t2v",
  method="POST",
  body={
    "prompt": "a bird taking flight from a branch in slow motion",
    "duration": "8",
    "aspectRatio": "16:9",
    "outputResolution": "1080p"
  }
)
```

Required: `prompt`, `duration`, `aspectRatio`, `outputResolution`. Options: `duration`: "4"-"15". `aspectRatio` (camelCase): "1:1", "3:4", "4:3", "16:9", "9:16", "21:9". `outputResolution`: "480p", "720p", "1080p". Optional `upscaleResolution`: "4k". For i2v, add `"image": "https://blob-url..."`. Unknown fields are rejected.

**seedance-fast** (Seedance 2 Fast) -- Same schema, cheaper and faster; `outputResolution` maxes out at "720p".

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

Options: `duration`: "3", "6", "9", "12", "15". `resolution`: "480p", "720p". `aspect_ratio`: "1:1", "16:9", "9:16", "4:3", "3:4", "3:2", "2:3". Optional `image` URL for image-to-video.

**wan-2.6** -- Budget, supports t2v and i2v. `duration`: "5", "10", "15". t2v uses `size` ("1280*720", "720*1280", "1920*1080", "1080*1920"); i2v uses `resolution` ("720p", "1080p") and `image`.

**sora-2** -- `seconds`: "4", "8", "12". `size`: "1280x720", "720x1280". Optional `input_reference` image URL for image-to-video.

**sora-2-pro** -- Premium. Same `seconds` as sora-2 ("4", "8", "12"); `size` adds "1792x1024", "1024x1792".

## Job Polling

Generation returns a `jobId` and `pollUrl`. Poll until complete:

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/jobs/{jobId}"
)
```

Poll images every 3s, videos every 10s. Result contains `imageUrl` or `videoUrl` (SVG content for vectorize jobs).
`agentcash.fetch` handles both paid submission requests and free SIWX polling requests.

Other job endpoints (SIWX, free): `GET /api/jobs` lists all your jobs; `DELETE /api/jobs/{jobId}` soft-deletes a failed job.

## Image Editing

Requires uploading the source image first. See [rules/uploads.md](rules/uploads.md).

All image models support `/edit`. The edit endpoint accepts `images` (array of blob URLs) plus a `prompt`.

```mcp
agentcash.fetch(
  url="https://stablestudio.dev/api/generate/gpt-image-2/edit",
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
| gpt-image-2 | $0.01-0.21 | minutes | Best default; poll, don't resubmit |
| gpt-image-1.5 | dynamic | ~3s | Fast GPT images, transparent backgrounds |
| nano-banana-pro | dynamic | ~10s | High quality, up to 4K resolution |
| nano-banana | dynamic | ~5s | Quick drafts, extreme aspect ratios |
| grok | dynamic | ~3s | Fast, many aspect ratios |
| flux-2-pro | dynamic | ~5s | High quality, resolution control |
| flux-2-max | ~$0.07+ | — | Top Flux quality, up to 4 MP |

### Video Models

| Model | Cost | Speed | Best For |
|-------|------|-------|----------|
| veo-3.1 | dynamic | 1-2min | Best quality, interpolation, references |
| veo-3.1-fast | dynamic | ~30s | Quick high-quality video |
| grok-video | dynamic | ~17s | Cheapest short videos |
| seedance | up to $20 | ~1min | High quality, up to 15s, 4K upscale |
| seedance-fast | up to $20 | ~40s | Budget video with decent quality |
| wan-2.6 | dynamic | 2-5min | Budget, text or image input, audio sync |
| sora-2 | dynamic | 1-3min | Premium quality |
| sora-2-pro | dynamic | 2-5min | Highest quality |
