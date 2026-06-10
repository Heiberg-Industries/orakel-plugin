# Orakel Plugin for Claude

Nordic business intelligence directly in Claude. Search 5M+ companies across Norway, Finland, and Sweden — firmographics, financials, ownership networks, corporate groups, and CRM push.

## What this plugin includes

**MCP connector** — connects Claude to `https://orakel.cloud/api/mcp` with OAuth authentication. Gives Claude access to 15 tools covering company lookup, search, financials, shareholders, corporate groups, procurement, inspections, and CRM export.

**Skills (slash commands):**

| Command | What it does |
|---|---|
| `/orakel:prospect` | Find and qualify Nordic prospects by industry, region, and size |
| `/orakel:company-deep-dive` | Full research report on a single company |
| `/orakel:market-scan` | Analyze a market segment — size, top players, tech landscape |
| `/orakel:ownership-map` | Trace ownership networks and corporate hierarchies |
| `/orakel:push-to-crm` | Export companies to Attio, HubSpot, or webhooks |

## Installation (Claude Code)

```bash
claude plugin install https://github.com/Heiberg-Industries/orakel-plugin
```

Or install locally from this directory:

```bash
claude plugin install ./plugin
```

After installing, Claude will prompt you to connect your Orakel account via OAuth. If you don't have an account, a 7-day trial is created automatically.

## Submitting to the Claude plugin directory

The plugin directory is accessible from Claude.ai Settings > Connectors (requires Team or Enterprise plan). To submit:

1. Run `claude plugin validate` against the plugin directory to catch any schema issues
2. Log in to Claude.ai with a Team/Enterprise account
3. Go to **Settings > Admin Settings > Connectors > Submit**
4. Provide this repository URL: `https://github.com/Heiberg-Industries/orakel-plugin`

Anthropic performs automated and manual review. Approved plugins appear in the connector directory for all Claude users.

## Connector directory (MCP server only)

The MCP server (`https://orakel.cloud/api/mcp`) can also be submitted separately to the Anthropic connector directory (which powers the Claude.ai web/mobile connector list). That process is at `https://claude.ai/docs/connectors/directory` and is handled by the Anthropic connectors team.

## OAuth details

- **Discovery:** `https://orakel.cloud/.well-known/oauth-authorization-server`
- **Callback (claude.ai/Desktop/mobile):** `https://claude.ai/api/mcp/auth_callback`
- **Callback (Claude Code):** `http://localhost:<ephemeral-port>/callback` (RFC 8252 loopback)
- **PKCE:** S256 required
- **DCR:** Supported at `https://orakel.cloud/api/oauth/register`

## Notes

- The `mcp__orakel__*` tool references in skills assume the MCP server is named `orakel` (which it is when installed via this plugin — the name comes from the key in `.mcp.json`)
