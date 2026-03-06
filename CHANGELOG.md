# Changelog

All notable changes to the LightSync Pro MCP server are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [2.8.0] - 2026-03-06

### Added
- Full broker-hosted MCP transport (SSE + Streamable HTTP)
- `syncific_status` — live connection health with source/destination map
- `site_health` — broken images, missing alt text, unoptimized files, orphaned attachments
- `ai_insights` — top quality scores, hero picks, A/B test results
- `version_test` — image version history and A/B test management
- `propose_action` — queue AI recommendations for human approval in WP dashboard
- AI Assistant toolbar badge for MCP-connected users

### Changed
- MCP transport moved from broker-hosted to site-hosted (eliminates broker round-trips)
- Tool definitions reordered into priority tiers to resolve Claude.ai 35-tool load limit
- `sync_info diff` now correctly compares content hashes (fixes Dropbox sync_diff bug)

### Fixed
- Dropbox path handling for root-level folders
- Lightroom catalog ID resolution (single catalog vs array response)
- Figma API endpoint corrections
- Shutterstock thumbnail hydration

---

## [2.3.0] - 2026-01-15

### Added
- Library Health Dashboard
- Hero Picker feature
- Media Library AI filtering
- AI-powered naming and alt text controls across all sources
- Protection flags (`_lightsync_ai_alt_text`, `_lightsync_ai_title`) to prevent sync from overwriting AI content
- Stress test demo injection system for admin panel

---

## [2.1.4] - 2025-12-01

### Added
- Shutterstock integration (live)
- AI image generation via OpenRouter (DALL-E, Flux, Stable Diffusion)
- Syncific Hub enterprise interface with dark hero header, KPI pills, site detail drawer
- Cross-network JavaScript tracking for Hub destination sites
- HubSpot and Contentful as destination platforms

### Fixed
- Shopify OAuth flow corrections
- HMAC verification fixes
- Compliance webhook registration

---

## [2.0.0] - 2025-10-01

### Added
- Broker-based OAuth architecture (Patent Application 19/440,404)
- Lightroom source (Adobe-approved after 3-month process)
- Figma source
- Dropbox source
- WordPress destination
- Shopify destination
- Free-to-Pro upgrade path with conflict prevention and data migration
- Free Canva and Figma WordPress plugins using the broker architecture
