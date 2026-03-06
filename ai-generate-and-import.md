# Example: Generate an AI Image and Import to WordPress

This walkthrough shows how to generate an AI image via OpenRouter and import it directly into your WordPress media library — all without leaving the conversation.

---

## Scenario

You need a hero image for a blog post about syncing creative assets to WordPress. You want to generate it with AI, review it, then import it directly.

---

## Step 1: Generate the Image

**Prompt:** "Generate a hero image for a blog post about syncing Lightroom photos to WordPress. Wide format, clean and modern."

**Tool:** `ai_generate_image`
```json
{
  "prompt": "Professional photographer syncing photos from Lightroom to a WordPress website on a modern desktop setup, clean minimal workspace, natural light, wide angle, editorial photography style",
  "aspect_ratio": "16:9",
  "quality": "standard"
}
```

Claude returns the generated image inline as an image content block — you can see it immediately in the conversation.

---

## Step 2: Import to WordPress

**Prompt:** "Import that to WordPress with the title 'Sync Lightroom to WordPress' and appropriate alt text"

**Tool:** `ai_generate_image`
```json
{
  "prompt": "Professional photographer syncing photos from Lightroom to a WordPress website on a modern desktop setup, clean minimal workspace, natural light, wide angle, editorial photography style",
  "aspect_ratio": "16:9",
  "quality": "standard",
  "auto_import": true,
  "destination": "wp"
}
```

**Response:**
```json
{
  "success": true,
  "attachment_id": 2720,
  "url": "https://yoursite.com/wp-content/uploads/2026/03/ai-generated-hero.avif",
  "title": "Sync Lightroom to WordPress",
  "width": 1920,
  "height": 1080,
  "mime_type": "image/avif"
}
```

The image is automatically converted to AVIF and added to the media library.

---

## Step 3: Set as Featured Image

**Prompt:** "Set that as the featured image on post ID 847"

**Tool:** `media_manage`
```json
{
  "action": "set_featured",
  "attachment_id": 2720,
  "post_id": 847,
  "confirmed": true
}
```

---

## Step 4: Create an A/B Test Variant (Optional)

**Prompt:** "Generate a second version with a different style so I can A/B test them"

**Tool:** `ai_generate_image`
```json
{
  "prompt": "Abstract visualization of cloud syncing to a CMS, flowing lines of data between cloud and browser, blue and purple gradient, minimal flat design",
  "aspect_ratio": "16:9",
  "auto_import": true,
  "destination": "wp"
}
```

Then create the test:

**Tool:** `version_test`
```json
{
  "action": "ab_create",
  "image_a": 2720,
  "image_b": 2721,
  "name": "Blog Hero - March 2026",
  "position": "hero",
  "confirmed": true
}
```

LightSync Pro will track impressions and CTR. When one variant pulls ahead, it auto-promotes (or queues for approval, depending on your config).

---

## Result

- AI image generated and previewed inline
- Imported to WordPress as AVIF
- Set as featured image on the target post
- A/B test running with automatic winner detection
