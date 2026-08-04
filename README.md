# 1acre MCP

The official MCP (Model Context Protocol) server for [1acre.in](https://1acre.in) — India's verified land marketplace. Connect Claude Code, Cursor, Claude Desktop, or any MCP-compatible client to look up survey numbers, browse cadastral data, and generate land-intelligence reports on behalf of your 1acre account.

> **This repository contains the client-side configuration and documentation** — how to connect. The server itself runs at `https://mcp.1acre.in`.

---

## What you can do

Once connected, any MCP client can call these tools as the signed-in 1acre user:

| Tool | Free | Description |
|---|---|---|
| `get_lookup_quota` | ✅ | Check how many survey lookups remain on your account |
| `browse_locations` | ✅ | Walk the location hierarchy (state → district → mandal → village) |
| `list_survey_numbers` | ✅ | List survey numbers that exist in a given village |
| `create_survey_number_report` | ⚠ Costs 1 lookup | Generate a full report for a survey number |
| `identify_parcel_at_point` | ⚠ Costs 1 lookup | Identify the parcel at a lat/lng coordinate |

Regular 1acre accounts get 3 lifetime lookups. `create_survey_number_report` and `identify_parcel_at_point` permanently consume one each. Free tools have no cost.

---

## Quick start — Claude Code

```bash
claude mcp add --scope user --transport http 1acre https://mcp.1acre.in/mcp \
  --callback-port 8118 \
  --client-id <your OAuth client id from Clerk dashboard>
```

Then in Claude Code:

1. Type `/mcp` — you'll see `1acre` with **Needs authentication**
2. Click **Sign In**
3. Browser opens 1acre's sign-in page (phone number)
4. Enter your phone number → receive OTP via SMS → enter code
5. Consent screen → **Allow**
6. `1acre` flips to **✓ Connected**

Now ask Claude anything land-related: *"How many survey lookups do I have left?"*, *"What districts does Telangana have?"*, etc.

---

## Quick start — Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or the equivalent path on your OS:

```json
{
  "mcpServers": {
    "1acre": {
      "url": "https://mcp.1acre.in/mcp",
      "transport": "http",
      "oauth": {
        "clientId": "<your OAuth client id>",
        "callbackPort": 8118
      }
    }
  }
}
```

Restart Claude Desktop. Sign in via the MCP servers dialog.

---

## Quick start — Cursor

Cursor's MCP config lives at `~/.cursor/mcp.json`. Add:

```json
{
  "mcpServers": {
    "1acre": {
      "url": "https://mcp.1acre.in/mcp"
    }
  }
}
```

Cursor prompts you to authenticate on first tool call.

More detailed examples in [examples/](./examples).

---

## Authentication

1acre MCP uses **OAuth 2.1 with PKCE**. Sign-in is phone-number-based via one-time SMS verification (secure, DLT-compliant for Indian phone numbers).

- Your phone number becomes your identity.
- If you already have a 1acre.in account, MCP resolves to the same account.
- If you don't, an account is auto-created on first sign-in.
- Sessions are managed by the auth server; your MCP client refreshes tokens automatically.

For architectural detail on the OAuth flow, see [docs/INTEGRATION_GUIDE.md](./docs/INTEGRATION_GUIDE.md).

---

## Registering an OAuth client

The `client_id` you use in the config comes from 1acre's OAuth provider. To request one:

1. Visit https://1acre.in/developers (or contact `hello@1acre.co`)
2. Provide your MCP client name and expected callback URL
3. Receive a `client_id` (public — safe to commit; PKCE-only, no secret)

For MCP clients that support Dynamic Client Registration (RFC 7591), no manual step is needed — the client will register itself.

---

## Available at

- **MCP endpoint:** `https://mcp.1acre.in/mcp`
- **Discovery:** `https://mcp.1acre.in/.well-known/oauth-protected-resource`
- **Health:** `https://mcp.1acre.in/healthz`

---

## Costs & limits

- **Free tools** (browse, list, quota check): unlimited
- **Paid tools** (report generation, parcel identification): 3 lifetime lookups per regular account
- **Rate limits**: 60 requests/minute per token

Upgrade an account for higher limits at [1acre.in/subscribe](https://1acre.in/subscribe).

---

## Support

- Docs: [1acre.in/developers/mcp](https://1acre.in/developers/mcp)
- Issues: [github.com/1acre-in/1acre-mcp/issues](https://github.com/1acre-in/1acre-mcp/issues)
- Email: `hello@1acre.co`

---

## License

MIT — see [LICENSE](./LICENSE).

Server code is not open source; this repo covers client-side configuration and documentation only.
