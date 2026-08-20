# 1acre MCP

The official MCP (Model Context Protocol) server for [1acre.in](https://1acre.in) — India's verified land marketplace. Connect Claude Code, Cursor, Claude Desktop, or any MCP-compatible client to look up survey numbers, browse cadastral data, and generate land-intelligence reports on behalf of your 1acre account.

> **This repository contains the client-side configuration and documentation** — how to connect. The server itself runs at `https://mcp.1acre.in`.

---

## What you can do

Once connected, any MCP client can call these tools as the signed-in 1acre user:

| Tool | Free | Description |
|---|---|---|
| `get_token_balance` | ✅ | Check your token balance, tier allowance, and what each paid tool costs |
| `browse_locations` | ✅ | Walk the location hierarchy (state → district → mandal → village) |
| `list_survey_numbers` | ✅ | List survey numbers that exist in a given village |
| `create_survey_number_report` | ⚠ Costs 10 tokens | Generate a full report for a survey number |
| `identify_parcel_at_point` | ⚠ Costs 10 tokens | Identify the parcel at a lat/lng coordinate |
| `get_survey_number_report` | ✅ | Re-open a previously generated report by ID — no token consumed |
| `get_report_coordinates` | ✅ | Get GeoJSON boundary coordinates for a previously generated report |

Paid tools spend from your **"1a" token balance**: `create_survey_number_report` and `identify_parcel_at_point` cost **10 tokens** per run each. Free tools cost nothing — once a report exists, you can re-open it and pull its boundary coordinates as often as you like without spending another token.

Call `get_token_balance` first (it's free) to see your live balance and the current per-tool price. A run that would overdraw fails with `insufficient_tokens` and is recoverable — top up from the **My Tokens** page on 1acre and retry.

> "Tokens" here means 1acre land-intelligence credits — unrelated to the OAuth access tokens your MCP client manages for sign-in.

---

## Quick start — Claude Code

```bash
claude mcp add --scope user --transport http 1acre https://mcp.1acre.in/mcp \
  --callback-port 8118 \
  --client-id eKg8BebDnd09zxwy
```

Then in Claude Code:

1. Type `/mcp` — you'll see `1acre` with **Needs authentication**
2. Click **Sign In**
3. Browser opens 1acre's sign-in page (phone number)
4. Enter your phone number → receive OTP via SMS → enter code
5. Consent screen → **Allow**
6. `1acre` flips to **✓ Connected**

Now ask Claude anything land-related: *"How many 1acre tokens do I have left?"*, *"What districts does Telangana have?"*, etc.

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
        "clientId": "eKg8BebDnd09zxwy",
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
- **You must have a 1acre.in account first** — sign up at [1acre.in](https://1acre.in) with the phone number you plan to use. MCP resolves your Clerk identity to that Account by matching the phone.
- Sessions are managed by the auth server; your MCP client refreshes tokens automatically.

---

## Registering an OAuth client

**Most clients register automatically.** 1acre's OAuth provider supports [RFC 7591 Dynamic Client Registration](https://datatracker.ietf.org/doc/html/rfc7591) at `https://clerk.1acre.in/oauth/register` — MCP clients that speak DCR (Claude Code, Smithery, and others) discover this from `.well-known/oauth-authorization-server` and register themselves on first connect. You don't need to do anything, and the `--client-id` flag in the Quick Start above is only needed if your client can't do DCR.

<details>
<summary>Manual registration (only if your client doesn't support DCR)</summary>

Contact `hello@1acre.in` with:

1. Your MCP client name (e.g. "Internal agent X")
2. The exact callback URL your client will use

You'll receive a `client_id` (public — safe to commit; PKCE-only, no client secret).

</details>

---

## Available at

- **MCP endpoint:** `https://mcp.1acre.in/mcp`
- **Discovery:** `https://mcp.1acre.in/.well-known/oauth-protected-resource`
- **Health:** `https://mcp.1acre.in/healthz`

---

## Costs & limits

- **Free tools** (browse, list, balance check): unlimited
- **Paid tools** (report generation, parcel identification): **10 tokens per run**
- **Rate limits**: 60 requests/minute per OAuth access token

Token allowance by account tier:

| Tier | Allowance |
|---|---|
| Non-premium (regular, free trial, expired premium) | 100 tokens |
| Premium — quarterly plan | 500 tokens |
| Premium — yearly plan | 2,000 tokens |
| Premium — grandfathered lifetime | 1,000 tokens |

Tokens **expire 365 days after they are credited** — this is a balance, not a lifetime allowance. Purchased top-up packs stack on top of your tier allowance, so a balance above the figures above is normal. `get_token_balance` is always the source of truth for what's actually spendable.

For higher limits, contact `hello@1acre.in`.

---

## Support

- Issues: [github.com/1acre-in/1acre-mcp/issues](https://github.com/1acre-in/1acre-mcp/issues)
- Email: `hello@1acre.in`

---

## License

MIT — see [LICENSE](./LICENSE).

Server code is not open source; this repo covers client-side configuration and documentation only.
