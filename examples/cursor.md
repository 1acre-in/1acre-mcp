# Cursor — connect to 1acre MCP

## Config location

`~/.cursor/mcp.json` (create the file if it doesn't exist).

## Config

```json
{
  "mcpServers": {
    "1acre": {
      "url": "https://mcp.1acre.in/mcp"
    }
  }
}
```

Cursor discovers the OAuth authorization server automatically via `.well-known/oauth-protected-resource` on the MCP endpoint, and does Dynamic Client Registration (RFC 7591) so you don't need to manually provide a `client_id`.

## Sign in

1. Reload Cursor after saving `mcp.json` (or open a fresh window).
2. Trigger a 1acre tool call from Composer — Cursor will detect the auth requirement and open a browser tab.
3. Complete the phone-OTP sign-in on 1acre's page.
4. Return to Cursor — the tool call resumes automatically.

## Verify

In Composer:

> `@1acre get_lookup_quota`

Expected: your current quota returned as a text block.

## Notes

- Cursor caches the OAuth token per workspace. If you sign in on one workspace it won't be shared with another.
- Refresh tokens are used automatically. If you see repeated auth prompts, check that your Cursor version supports MCP OAuth (available since Cursor 0.42).
