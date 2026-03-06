# Contributing to LightSync Pro MCP

Thanks for your interest in contributing. LightSync Pro MCP is the AI interface layer for the LightSync Pro ecosystem — contributions that improve tool reliability, add source coverage, or improve developer experience are welcome.

---

## What We're Looking For

- **Bug reports** — especially around tool parameter handling, auth edge cases, or MCP spec compliance
- **Source integrations** — adapters for new creative asset sources (Getty, Adobe Stock, Frame.io, etc.)
- **Destination integrations** — new CMS/ecommerce targets beyond WordPress, Shopify, Contentful, HubSpot
- **Example walkthroughs** — real-world prompt → tool call → result documentation in `/examples`
- **Schema improvements** — better descriptions, enum values, or input validation in tool definitions

---

## Getting Started

### Prerequisites

- WordPress site with LightSync Pro Pro installed and active
- Access to at least one connected source (Lightroom, Dropbox, Figma, or Shutterstock)
- PHP 8.0+
- An MCP-compatible client (Claude Desktop, Cursor, Windsurf, or Claude.ai connector)

### Local Setup

1. Clone the repo
2. Copy `config-examples/claude-desktop.json` and update the URL to point at your dev site
3. Connect your MCP client to your local WordPress install
4. Use `syncific_status` to confirm the connection

---

## How to Submit

1. Fork the repository
2. Create a branch: `git checkout -b feature/your-feature-name`
3. Make your changes with clear commit messages
4. Open a pull request with a description of what changed and why
5. Reference any related issues

---

## Code Standards

- Tool definitions live in `lsp_mcp_tool_definitions()` — keep descriptions concise and accurate
- Tool execution cases in `lsp_mcp_execute_tool()` — follow the existing `$make_req` / `$get_data` pattern
- All logging must route through `Logger::debug` (silent in production)
- Never log raw OAuth tokens, broker responses, or Authorization headers
- Redact credentials from all debug output

---

## Reporting Security Issues

Please do **not** open public issues for security vulnerabilities. Email support@lightsyncpro.com directly. Include steps to reproduce and any relevant context.

---

## Questions

Open a discussion or reach out at [lightsyncpro.com](https://lightsyncpro.com).
