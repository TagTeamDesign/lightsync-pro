# Example: Media Library Health Check & Optimization

This walkthrough shows how to audit your WordPress media library for issues and fix them using the MCP.

---

## Scenario

You want a quick health check on your site's media library — find broken images, missing alt text, and unoptimized files, then fix everything in one session.

---

## Step 1: Run a Full Health Check

**Prompt:** "Run a full health check on my media library"

**Tool:** `site_health`
```json
{ "check": "all" }
```

**Response:**
```json
{
  "broken_images": { "count": 0 },
  "missing_alt": {
    "count": 14,
    "items": [
      { "attachment_id": 2505, "title": "hero-banner-march" },
      { "attachment_id": 2504, "title": "product-shot-01" }
    ]
  },
  "unoptimized": {
    "count": 3,
    "items": [
      { "attachment_id": 2271, "title": "LightSync Pro Demo - Page 2", "mime_type": "image/png", "filesize_kb": 247 },
      { "attachment_id": 2270, "title": "LightSync Pro Demo - Page 1", "mime_type": "image/png", "filesize_kb": 299 }
    ]
  },
  "orphaned": { "count": 0 }
}
```

---

## Step 2: Fix Unoptimized Images

**Prompt:** "Convert those PNG files to AVIF"

**Tool:** `media_manage`
```json
{
  "action": "re_optimize",
  "attachment_ids": [2271, 2270],
  "confirmed": true
}
```

**Response:**
```json
{
  "success": true,
  "message": "Re-optimize: 2 converted to modern format",
  "result": { "converted": 2, "skipped": 0 }
}
```

Saves ~550KB off two files alone.

---

## Step 3: Apply AI Alt Text

**Prompt:** "Apply AI alt text to all images that are missing it"

**Tool:** `media_manage`
```json
{
  "action": "bulk_ai_alt",
  "confirmed": true
}
```

**Response:**
```json
{
  "success": true,
  "message": "Applied AI-suggested alt text to 14 image(s)",
  "result": {
    "updated": 14,
    "images": [
      {
        "attachment_id": 2505,
        "title": "hero-banner-march",
        "alt_text": "LightSync Pro dashboard showing Lightroom sync in progress with AVIF optimization stats."
      }
    ]
  }
}
```

> **Note:** Only images without the `_lightsync_ai_alt_text` protection flag are updated. Manually-written alt text is never overwritten.

---

## Step 4: Get an Optimization Report

**Prompt:** "Show me the full optimization report"

**Tool:** `reports`
```json
{ "action": "optimization" }
```

**Response:**
```json
{
  "total_lightsync_images": 71,
  "total_disk_usage": "4.9 MB",
  "by_format": [
    { "format": "image/avif", "count": 68, "total_size": "4.7 MB" },
    { "format": "image/webp", "count": 2, "total_size": "150 KB" },
    { "format": "image/svg+xml", "count": 1 }
  ],
  "by_source": [
    { "source": "lightroom", "count": 31, "optimization_pct": "100%" },
    { "source": "dropbox", "count": 9, "optimization_pct": "100%" },
    { "source": "figma", "count": 9, "optimization_pct": "100%" }
  ]
}
```

---

## Result

In one session:
- 2 PNGs converted to AVIF (~550KB saved)
- 14 images got AI-generated alt text
- 0 broken images, 0 orphaned attachments
- 96% of LightSync images now in AVIF format
