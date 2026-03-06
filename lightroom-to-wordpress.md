# Example: Sync a Lightroom Album to WordPress

This walkthrough shows a complete end-to-end sync from Adobe Lightroom to the WordPress media library, including finding unsynced photos and bulk importing them.

---

## Scenario

You've been editing a product shoot in Lightroom and want to get the final selects into WordPress — optimized, with AI-generated alt text, ready to publish.

---

## Step 1: Check Connection

**Prompt:** "Check my LightSync status"

**Tool:** `syncific_status`

**Response:**
```json
{
  "site_url": "https://yoursite.com",
  "plan": "pro",
  "sources": { "lightroom": true },
  "connected": ["lightroom"]
}
```

---

## Step 2: Browse Albums

**Prompt:** "Show me my Lightroom albums"

**Tool:** `lightroom_list_albums`

**Response:**
```json
{
  "_catalog_id": "catalog:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resources": [
    { "id": "album:aaa111", "payload": { "name": "Product Shoot March 2026", "assetCount": 48 } },
    { "id": "album:bbb222", "payload": { "name": "Hero Images 2025", "assetCount": 12 } }
  ]
}
```

---

## Step 3: Find Unsynced Photos

**Prompt:** "What photos from 'Product Shoot March 2026' haven't been synced yet?"

**Tool:** `sync_info`
```json
{
  "action": "diff",
  "source": "lightroom",
  "catalog_id": "catalog:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "source_path": "album:aaa111"
}
```

**Response:**
```json
{
  "unsynced": 23,
  "synced": 25,
  "items": [
    { "source_id": "asset:img001", "filename": "DSC_0142.jpg" },
    { "source_id": "asset:img002", "filename": "DSC_0143.jpg" }
  ]
}
```

---

## Step 4: Browse with Thumbnails

**Prompt:** "Show me the unsynced photos with previews"

**Tool:** `lightroom_get_photos`
```json
{
  "catalog_id": "catalog:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "album_id": "album:aaa111",
  "limit": 23
}
```

Claude returns the photos as inline image content blocks — you can visually review before importing.

---

## Step 5: Bulk Import

**Prompt:** "Import all 23 unsynced photos to WordPress"

**Tool:** `bulk_import`
```json
{
  "source": "lightroom",
  "source_ids": ["asset:img001", "asset:img002"],
  "catalog_id": "catalog:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "sync_target": "wp",
  "confirmed": true
}
```

Each photo goes through the full pipeline: download → resize to max 2048px → AVIF conversion → dedup check → WordPress media library.

---

## Step 6: Apply AI Alt Text

**Prompt:** "Apply AI alt text to the newly imported images"

**Tool:** `media_manage`
```json
{
  "action": "bulk_ai_alt",
  "source": "lightroom",
  "confirmed": true
}
```

**Response:**
```json
{
  "success": true,
  "message": "Applied AI-suggested alt text to 23 image(s)"
}
```

---

## Result

23 Lightroom photos are now in your WordPress media library:
- Resized to 2048px max
- Converted to AVIF (typically 60–80% smaller than original JPEG)
- AI-generated alt text applied
- Dedup-tracked so re-syncing won't create duplicates
