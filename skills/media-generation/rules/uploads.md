# File Uploads

Upload images for editing or image-to-video. Cost: $0.01 per upload.

## Upload Flow

**1. Get upload token** ($0.01)
```bash
npx agentcash fetch https://stablestudio.dev/api/x402/uploads -m POST -b '{"filename": "image.png", "contentType": "image/png"}'
```

Returns: `{uploadId, clientToken, pathname}`

**2. Upload file to Vercel Blob**
```bash
curl -X PUT "https://vercel.com/api/blob/?pathname={pathname}" \
  -H "authorization: Bearer {clientToken}" \
  -H "x-content-type: image/png" \
  -H "x-api-version: 11" \
  --data-binary @image.png
```

Returns: `{url: "https://....blob.vercel-storage.com/..."}`

**3. Confirm upload**
```bash
npx agentcash fetch https://stablestudio.dev/api/x402/uploads/confirm -m POST -b '{"uploadId": "...", "blobUrl": "https://..."}'
```

Use the `blobUrl` in edit/i2v requests.
