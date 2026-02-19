---
name: upload-and-share
description: Upload any file and get a public URL. Pay-per-upload file hosting with 6-month TTL — $0.02 for 10MB, $0.20 for 100MB, $2.00 for 1GB. Use when asked to upload a file, host a file, get a shareable link, make a file publicly accessible, or store a file in the cloud.
---

# StableUpload — File Hosting

Pay-per-upload file hosting. Upload any file, get a public URL.

## Upload Flow

### Step 1: Buy upload slot (paid)

```bash
npx agentcash fetch https://stableupload.dev/api/upload -m POST -b '{"filename": "photo.png", "contentType": "image/png", "tier": "10mb"}'
```

**Tiers:**
| Tier | Max Size | Price |
|---|---|---|
| `10mb` | 10 MB | $0.02 |
| `100mb` | 100 MB | $0.20 |
| `1gb` | 1 GB | $2.00 |

Returns:
```json
{
  "uploadId": "k7gm3nqp2",
  "uploadUrl": "https://f.agentupload.dev/k7gm3nqp2/photo.png?t=...",
  "publicUrl": "https://f.agentupload.dev/k7gm3nqp2/photo.png",
  "expiresAt": "2025-08-01T00:00:00.000Z",
  "maxSize": 10485760
}
```

### Step 2: Upload file via returned URL

```bash
curl -X PUT "$uploadUrl" -H "Content-Type: image/png" --data-binary @photo.png
```

### Step 3: File is live

The `publicUrl` is permanent for the upload's lifetime (6 months). Share it anywhere.

## Other Endpoints

```bash
npx agentcash fetch https://stableupload.dev/api/uploads           # List all uploads for your wallet (free)
npx agentcash fetch https://stableupload.dev/api/download/UPLOAD_ID # Get details for a specific upload (free)
```

## Tips

- Upload URLs expire after 1 hour — upload promptly after buying a slot
- Any file type accepted — `contentType` is advisory
- Public URLs use CloudFront CDN — fast global access
- Files expire 6 months after upload
- Choose the smallest tier that fits your file to save money
