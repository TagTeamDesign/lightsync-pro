# Example: Distribute Assets Across Hub Sites

This walkthrough shows how to import an asset from Dropbox and distribute it to multiple WordPress sites via the Syncific Hub.

---

## Scenario

You manage 6 client WordPress sites through Syncific Hub. A new brand asset has been dropped into a shared Dropbox folder and needs to go to all of them.

---

## Step 1: Browse Dropbox

**Prompt:** "Show me what's in the /brand-assets Dropbox folder"

**Tool:** `source_browse`
```json
{
  "source": "dropbox",
  "id": "/brand-assets"
}
```

**Response:**
```json
{
  "entries": [
    { "id": "id:abc123", "name": "brand-logo-2026.png", "size": 284000, ".tag": "file" },
    { "id": "id:abc124", "name": "hero-banner-q1.jpg", "size": 1200000, ".tag": "file" }
  ]
}
```

---

## Step 2: Import to Hub Controller

**Prompt:** "Import brand-logo-2026.png to WordPress"

**Tool:** `bulk_import`
```json
{
  "source": "dropbox",
  "source_id": "id:abc123",
  "sync_target": "wp",
  "title": "Brand Logo 2026",
  "alt_text": "LightSync Pro logo 2026",
  "confirmed": true
}
```

**Response:**
```json
{
  "success": true,
  "attachment_id": 2722,
  "url": "https://yoursite.com/wp-content/uploads/2026/03/brand-logo-2026.avif",
  "mime_type": "image/avif"
}
```

---

## Step 3: List Hub Sites

**Prompt:** "Show me my hub sites"

**Tool:** `hub`
```json
{ "action": "list_sites" }
```

**Response:**
```json
{
  "sites": [
    { "id": "site_1", "name": "Client A", "url": "https://clienta.com" },
    { "id": "site_2", "name": "Client B", "url": "https://clientb.com" },
    { "id": "site_3", "name": "Client C", "url": "https://clientc.com" }
  ],
  "groups": [
    { "id": "grp_marketing", "name": "Marketing Sites", "site_count": 3 }
  ]
}
```

---

## Step 4: Distribute to All Sites

**Prompt:** "Distribute the brand logo to all hub sites"

**Tool:** `hub`
```json
{
  "action": "distribute",
  "attachment_id": 2722,
  "target_sites": ["all"],
  "confirmed": true
}
```

**Response:**
```json
{
  "job_id": "job_abc789",
  "status": "queued",
  "target_count": 6
}
```

---

## Step 5: Check Job Status

**Prompt:** "How's the distribution going?"

**Tool:** `hub`
```json
{
  "action": "job_status",
  "job_id": "job_abc789"
}
```

**Response:**
```json
{
  "job_id": "job_abc789",
  "status": "completed",
  "completed": 6,
  "failed": 0,
  "results": [
    { "site": "https://clienta.com", "attachment_id": 4421, "status": "success" },
    { "site": "https://clientb.com", "attachment_id": 1893, "status": "success" }
  ]
}
```

---

## Result

One Dropbox asset is now in all 6 hub sites:
- Converted to AVIF at each destination
- Dedup-tracked — re-running won't create duplicates
- All done in a single conversation without touching WP admin on any site
