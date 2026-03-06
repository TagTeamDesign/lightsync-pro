# Architecture

LightSync Pro MCP exposes your creative asset pipeline as an AI-native interface. This document explains the key architectural layers.

---

## Overview

```mermaid
graph TB
    subgraph AI["AI Client"]
        C[Claude / Cursor / Windsurf]
    end

    subgraph Broker["Syncific Broker (lightsyncpro.com)"]
        B[OAuth Broker]
        T[MCP Transport\nlsp-client/v1/mcp]
        S[JWT Auth]
    end

    subgraph WP["WordPress Site"]
        P[LightSync Pro Plugin]
        Q[Sync Queue]
        M[Media Library]
    end

    subgraph Sources["Creative Sources"]
        LR[Adobe Lightroom]
        FG[Figma]
        DB[Dropbox]
        SS[Shutterstock]
        CV[Canva]
    end

    subgraph Destinations["Destinations"]
        W[WordPress]
        SH[Shopify]
        H[Syncific Hub]
        CF[Contentful]
        HS[HubSpot]
    end

    C -->|MCP tool call| T
    T -->|Bearer JWT| S
    S --> B
    B -->|Proxied OAuth| LR
    B -->|Proxied OAuth| FG
    B -->|Proxied OAuth| DB
    B -->|Proxied OAuth| SS
    B -->|Proxied OAuth| CV
    P --> Q
    Q --> W
    Q --> SH
    Q --> H
    H --> CF
    H --> HS
```

---

## The Broker Architecture

The core innovation (Patent Application 19/440,404) is that **OAuth client secrets are held centrally in the broker**, not distributed with WordPress plugins.

This solves a fundamental problem: WordPress plugins can't safely hold OAuth secrets for cloud APIs like Adobe Lightroom. Anyone who installs the plugin would have access to the credentials — a critical security and compliance issue that prevents legitimate Adobe API approval at scale.

```mermaid
sequenceDiagram
    participant U as User
    participant WP as WordPress Plugin
    participant B as Syncific Broker
    participant LR as Adobe Lightroom

    U->>WP: Connect Lightroom
    WP->>B: Initiate OAuth (site_id, redirect)
    B->>LR: OAuth request (broker client_id/secret)
    LR->>U: Authorization page
    U->>LR: Approve
    LR->>B: Auth code
    B->>LR: Exchange for tokens (secret stays in broker)
    B->>WP: Site-scoped JWT (no raw tokens)
    WP->>B: API calls via JWT
    B->>LR: Proxied with real token
    B->>WP: Response
```

The plugin never sees the OAuth access token. It only holds a site-scoped JWT that the broker validates on each call.

---

## MCP Transport

The MCP server runs directly on the WordPress site under `lsp-client/v1/mcp`. It supports both:

- **Streamable HTTP** (POST with JSON-RPC → JSON-RPC response) — preferred for Claude.ai and modern clients
- **SSE legacy** (GET → event stream, POST /message) — for Claude Desktop and older clients

```mermaid
sequenceDiagram
    participant AI as AI Client
    participant MCP as MCP Endpoint\n/wp-json/lsp-client/v1/mcp
    participant B as Broker
    participant API as Source API

    AI->>MCP: POST initialize
    MCP->>AI: capabilities + Mcp-Session-Id

    AI->>MCP: POST tools/call (e.g. lightroom_list_albums)
    MCP->>B: Proxied API request (JWT auth)
    B->>API: Real API call
    API->>B: Response
    B->>MCP: Data
    MCP->>AI: JSON-RPC result with content blocks
```

---

## Tool Tiers

Tools are ordered by load priority to stay within Claude.ai's ~35-tool limit per session:

| Tier | Tools |
|------|-------|
| 1 — Connection | `syncific_status`, `syncific_connect` |
| 2 — Browse | `lightroom_list_albums`, `figma_list_files`, `dropbox_list_files`, etc. |
| 3 — Sync & Import | `sync_source`, `bulk_import`, `sync_info` |
| 4 — Media Management | `media_library`, `media_manage`, `site_health` |
| 5 — AI & Generation | `ai_generate_image`, `ai_generate_text`, `ai_insights` |
| 6 — Destinations | `shopify_upload_file`, `hub`, `version_test` |
| 7 — Config & Reporting | `site_config`, `reports`, `propose_action` |

---

## Data Flow: Sync Pipeline

```mermaid
flowchart LR
    A[Source Asset] --> B[Download via Broker]
    B --> C[Dedup Check\ncontent hash]
    C -->|New| D[Resize\nmax 2048px]
    C -->|Duplicate| Z[Skip]
    D --> E[AVIF/WebP\nConversion]
    E --> F[WordPress\nMedia Library]
    F --> G{Destinations}
    G --> H[WordPress]
    G --> I[Shopify Files]
    G --> J[Hub Distribution]
```

---

## Multi-Site Hub

The Syncific Hub extends the architecture to enterprise multi-site setups. A single hub controller site distributes assets to N destination sites, each running a lightweight receiver plugin.

```mermaid
graph TB
    H[Hub Controller\nlightsyncpro.com] --> S1[Destination Site 1]
    H --> S2[Destination Site 2]
    H --> S3[Destination Site 3]
    H --> S4[Destination Site N]
    
    subgraph Groups
        G1[Group: Marketing Sites]
        G2[Group: Client Sites]
    end
    
    H --> G1
    H --> G2
    G1 --> S1
    G1 --> S2
    G2 --> S3
    G2 --> S4
```
