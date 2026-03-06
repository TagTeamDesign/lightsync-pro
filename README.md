# Configuration Examples

Ready-to-use config snippets for connecting LightSync Pro MCP to your AI client.

Replace `YOUR_TOKEN` with the token from your LightSync Pro plugin settings under **AI & Tools → MCP Settings**.

---

## Claude.ai (Connector URL)

In Claude.ai settings → **Integrations** → **Add MCP Server**:

```
https://yoursite.com/wp-json/lsp-client/v1/mcp?token=YOUR_TOKEN
```

This uses the Streamable HTTP transport — no additional setup required.

---

## Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "lightsync-pro": {
      "type": "url",
      "url": "https://yoursite.com/wp-json/lsp-client/v1/mcp?token=YOUR_TOKEN"
    }
  }
}
```

---

## Cursor

Add to `.cursor/mcp.json` in your project root, or to the global config at `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "lightsync-pro": {
      "type": "url",
      "url": "https://yoursite.com/wp-json/lsp-client/v1/mcp?token=YOUR_TOKEN"
    }
  }
}
```

---

## Windsurf

Add to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "lightsync-pro": {
      "type": "url",
      "url": "https://yoursite.com/wp-json/lsp-client/v1/mcp?token=YOUR_TOKEN"
    }
  }
}
```

---

## Multiple Sites

You can connect multiple WordPress sites simultaneously. Each needs its own token:

```json
{
  "mcpServers": {
    "lightsync-main": {
      "type": "url",
      "url": "https://yoursite.com/wp-json/lsp-client/v1/mcp?token=TOKEN_1"
    },
    "lightsync-staging": {
      "type": "url",
      "url": "https://staging.yoursite.com/wp-json/lsp-client/v1/mcp?token=TOKEN_2"
    }
  }
}
```

> **Note:** Claude.ai currently deduplicates servers with identical tool names. If connecting multiple sites in Claude.ai, this is a known limitation — the workaround is to use one site at a time via the connector URL.

---

## Verifying the Connection

After configuring, ask your AI client:

> "Check my LightSync Pro connection status"

A successful response looks like:

```json
{
  "site_url": "https://yoursite.com",
  "plan": "pro",
  "connected": ["lightroom", "dropbox", "figma"]
}
```

If you get a 404, verify the plugin is active on your WordPress site and the token matches what's in the plugin settings.

---

## SSE Legacy Transport (Advanced)

For clients that require the SSE transport instead of Streamable HTTP:

```
https://yoursite.com/wp-json/lsp-client/v1/mcp/sse?token=YOUR_TOKEN
```

The SSE endpoint supports both GET (stream) and POST (message). Most modern clients should use the standard `/mcp` endpoint above.
