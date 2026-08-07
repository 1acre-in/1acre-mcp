# Claude Code — connect to 1acre MCP

## Add the server

```bash
claude mcp add --scope user --transport http 1acre https://mcp.1acre.in/mcp \
  --callback-port 8118 \
  --client-id eKg8BebDnd09zxwy
```

`--scope user` makes the server available from any project you open in Claude Code. Use `--scope project` if you only want it available in the current directory.

`--callback-port 8118` is where Claude Code listens for the OAuth redirect. Any free port works; the value must match a redirect URI registered on your OAuth application.

## Sign in

```
/mcp
```

Type this in Claude Code. Look for `1acre` in the list — it'll show `Needs authentication`.

Click it → Claude Code opens your default browser at 1acre's OAuth sign-in page.

- Enter your phone number
- Receive an OTP via SMS (India: through our DLT-compliant template)
- Enter the OTP
- Consent screen → **Allow**
- Browser redirects back → Claude Code flips to `✓ Connected`

## Verify it works

Ask Claude:

> "How many 1acre survey lookups do I have left?"

Expected response: `3 of 3 lookups remaining (0 used, account type: regular)` (or your actual quota).

## Remove

```bash
claude mcp remove 1acre --scope user
```

## Troubleshooting

**"Callback URL mismatch"** — your `client_id`'s allowed redirect URIs don't include `http://localhost:8118/callback`. Email `hello@1acre.co` with the exact callback URL you need registered.

**"No OTP arrived"** — check your phone signal, then check that the phone number is registered in the correct country format (`+91` for India). If still nothing, contact `hello@1acre.co`.

**"Signed in but tools return `Not authenticated to 1acre`"** — Your 1acre account may not be linked to the phone number you used. Sign in to [1acre.in](https://1acre.in) with the same phone first, then retry the MCP flow.

**Token expired mid-session** — Claude Code handles refresh automatically. If a tool call fails with 401, retry once; if it still fails, `/mcp` → click Reconnect on `1acre`.
