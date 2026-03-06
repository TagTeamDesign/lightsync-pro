# Tool Reference

Complete reference for all LightSync Pro MCP tools. 35 tools across 8 categories.

---

## Connection

### `syncific_status`
Check which sources are connected, destinations available, and current plan.

```json
{}
```

**Returns:** `site_url`, `plan`, `sources` map, `connected` array, `shopify`, `hub`

---

### `syncific_connect`
Initiate the OAuth connection flow. Opens a browser for authentication. Run this first if not connected.

```json
{}
```

---

## Sources: Browse

### `lightroom_list_albums`
List all Lightroom albums. Automatically resolves catalog ID.

```json
{}
```

**Returns:** Album names, IDs, photo counts, `_catalog_id` for use in subsequent calls.

---

### `lightroom_get_photos`
Get photos from a Lightroom album with thumbnails and EXIF metadata.

```json
{
  "catalog_id": "string (required)",
  "album_id": "string (required)",
  "limit": "number (default: 10, max: 50)"
}
```

---

### `figma_list_files`
List recent Figma files with names, IDs, and last modified dates.

```json
{
  "team_id": "string (optional)",
  "project_id": "string (optional)"
}
```

---

### `figma_get_frames`
Get frames/pages from a Figma file with dimensions and types.

```json
{
  "file_key": "string (required)"
}
```

---

### `dropbox_list_files`
List files and folders in a Dropbox path. Returns image download links.

```json
{
  "path": "string (default: root '')",
  "cursor": "string (pagination)"
}
```

---

### `shutterstock_list_collections`
List your Shutterstock lightboxes/collections.

```json
{
  "page": "number (default: 1)",
  "per_page": "number (default: 20)"
}
```

---

### `shutterstock_get_collection_items`
Get images from a Shutterstock collection with previews.

```json
{
  "collection_id": "string (required)",
  "page": "number",
  "per_page": "number"
}
```

---

### `shutterstock_list_licenses`
List your licensed/purchased Shutterstock images.

```json
{
  "page": "number",
  "per_page": "number"
}
```

---

### `canva_list_designs`
List your Canva designs with thumbnails. Returns design names, IDs, types, modification dates.

```json
{
  "continuation": "string (pagination token)"
}
```

---

### `canva_list_folders`
List your Canva folders.

```json
{
  "continuation": "string (pagination token)"
}
```

---

### `canva_export_design`
Export a Canva design. Returns an export job ID — use `canva_get_export` to poll status.

```json
{
  "design_id": "string (required)",
  "format": "png | jpg | pdf | svg | gif | pptx | mp4 (default: png)"
}
```

---

### `canva_get_export`
Check the status of a Canva export job and get the download URL when complete.

```json
{
  "export_id": "string (required)"
}
```

---

## Sync & Import

### `sync_source`
Trigger a full sync for a connected source. Finds all new/changed items and imports them.

```json
{
  "source": "lightroom | figma | dropbox | shutterstock | canva | bynder | brandfolder (required)",
  "source_path": "string (Lightroom album URL or Dropbox path)",
  "catalog_id": "string (Lightroom only)",
  "sync_target": "wp | shopify | both (default: wp)",
  "confirmed": true
}
```

---

### `sync_info`
Compare source against asset map to find unsynced items, or view sync history.

```json
{
  "action": "diff | log (required)",
  "source": "string",
  "catalog_id": "string (Lightroom diff only)",
  "source_path": "string (diff only)",
  "limit": "number (log only, default: 20)"
}
```

---

### `bulk_import`
Import one or more source assets through the full optimization pipeline. Max 50 per call.

```json
{
  "source": "dropbox | figma | canva | lightroom | shutterstock (required)",
  "source_ids": ["id1", "id2"],
  "source_id": "string (single asset)",
  "catalog_id": "string (Lightroom only)",
  "sync_target": "wp | shopify | both",
  "title": "string (single import only)",
  "alt_text": "string (single import only)",
  "confirmed": true
}
```

---

## Media Management

### `media_library`
Browse the WordPress media library. Filter, search, and sort by AI quality score.

```json
{
  "source": "lightroom | figma | canva | dropbox | shutterstock",
  "search": "string",
  "sort_by": "date | quality | hero | impressions",
  "limit": "number",
  "offset": "number"
}
```

---

### `media_manage`
Modify WordPress media. All write actions require `confirmed: true`.

| Action | Description |
|--------|-------------|
| `set_alt` | Set alt text on an attachment |
| `set_metadata` | Update title, caption |
| `set_featured` | Set as featured image on a post |
| `delete` | Delete an attachment |
| `bulk_ai_alt` | Apply AI-generated alt text to all unprotected images missing it |
| `re_optimize` | Re-convert images to AVIF/WebP |
| `shopify_sync` | Sync an attachment to Shopify |
| `ai_analyze` | Run AI quality scoring on an image |
| `find_usage` | Find all posts using an attachment |
| `swap_sitewide` | Replace all usage of one image with another |
| `gen_rollback` | Roll back to a previous AI-generated version |
| `approve_variant` | Approve an A/B test variant |
| `reject_variant` | Reject an A/B test variant |

---

### `site_health`
Scan the media library for issues.

```json
{
  "check": "all | broken | missing_alt | unoptimized | orphaned | summary",
  "limit": "number"
}
```

---

### `asset_map`
View which source assets have been synced to which destinations.

```json
{
  "source": "string",
  "destination": "wp | shopify",
  "source_id": "string"
}
```

---

## AI & Generation

### `ai_generate_image`
Generate an AI image via OpenRouter (DALL-E, Flux, Stable Diffusion, Gemini). Returns image inline.

```json
{
  "prompt": "string (required)",
  "model": "string (optional, uses configured default)",
  "aspect_ratio": "1:1 | 16:9 | 9:16 | 4:3 | 3:4",
  "quality": "standard | preview",
  "auto_import": "boolean (import directly to WP media library)",
  "destination": "wp | shopify | both | hub"
}
```

---

### `ai_generate_text`
Generate text content via AI. Ideal for alt text, captions, SEO descriptions.

```json
{
  "prompt": "string (required)",
  "model": "string (optional)"
}
```

---

### `ai_insights`
AI-powered analysis of your media library.

```json
{
  "insight_type": "top_quality | top_performers | hero_picks | needs_alt | ab_tests | summary",
  "source": "string (optional filter)"
}
```

---

### `version_test`
Image version history and A/B test management.

```json
{
  "action": "history | rollback | ab_create | ab_delete (required)",
  "attachment_id": "number",
  "target_version": "number (rollback only)",
  "image_a": "number (ab_create)",
  "image_b": "number (ab_create)",
  "name": "string (ab_create)",
  "position": "hero | thumbnail | gallery | any",
  "test_id": "string (ab_delete)",
  "confirmed": true
}
```

---

## Destinations

### `shopify_upload_file`
Upload an image directly to Shopify Files. Runs through AVIF/WebP optimization and dedup tracking.

```json
{
  "filename": "string (required)",
  "image_base64": "string (required)",
  "alt_text": "string",
  "source_id": "string (for dedup tracking)",
  "confirmed": true
}
```

---

### `hub`
Manage Syncific Hub multi-site distribution.

| Action | Description |
|--------|-------------|
| `list_sites` | List destination sites in your Hub network |
| `list_groups` | List site groups |
| `distribute` | Distribute an attachment to target sites or a group |
| `sync_image` | Sync a specific image to hub sites |
| `resync_all` | Resync all assets to hub |
| `job_status` | Check status of a distribution job |

```json
{
  "action": "required",
  "attachment_id": "number",
  "target_sites": ["https://site1.com", "https://site2.com"],
  "group_id": "string",
  "job_id": "string (job_status only)",
  "site_url": "string",
  "confirmed": true
}
```

---

## Config & Reporting

### `site_config`
View or update LightSync Pro configuration.

```json
{
  "action": "view | update | queue_status | queue_list | cache_clear (required)",
  "avif_enable": "boolean",
  "avif_quality": "number (1–100)",
  "webp_quality": "number (1–100)",
  "max_width": "number",
  "max_height": "number",
  "auto_sync": "boolean",
  "sync_interval": "string",
  "sync_target": "wp | shopify | hub",
  "confirmed": true
}
```

---

### `reports`
Generate optimization or inventory reports.

```json
{
  "action": "optimization | csv (required)",
  "source": "string (optional filter)",
  "limit": "number (csv only, default: 500)",
  "include_performance": "boolean (csv only)"
}
```

---

### `propose_action`
Queue an AI recommendation as an approve/reject card in the WordPress dashboard.

```json
{
  "action_type": "bulk_import | re_optimize | bulk_ai_alt | sync_source | health_fix | custom (required)",
  "label": "string (required)",
  "reason": "string",
  "priority": "high | medium | low",
  "payload": {},
  "source": "string"
}
```
