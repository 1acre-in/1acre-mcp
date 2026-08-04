# ChatGPT — connect to 1acre MCP

ChatGPT supports MCP servers via Custom GPTs and Actions in some rollouts. Availability varies by plan.

## Option A · ChatGPT Custom GPT with Actions

1. Create a new Custom GPT.
2. In the **Configure** tab, scroll to **Actions** → **Create new action**.
3. Set the schema URL to: `https://mcp.1acre.in/.well-known/openapi.json` (if published) or use the MCP endpoint directly.
4. Authentication: **OAuth**
   - Client ID: `<your_client_id>`
   - Client Secret: leave blank (PKCE, no secret needed)
   - Authorization URL: `https://<your-clerk-slug>.clerk.accounts.dev/oauth/authorize`
   - Token URL: `https://<your-clerk-slug>.clerk.accounts.dev/oauth/token`
   - Scope: `openid profile email offline_access`

5. Save. ChatGPT will show the OAuth authorization link on next tool invocation.

## Option B · ChatGPT native MCP support (if enabled)

If your ChatGPT plan has native MCP support (rolling out in 2026):

Settings → Connectors → Add MCP Server:

```
Name: 1acre
URL: https://mcp.1acre.in/mcp
```

Complete the OAuth flow in the popup.

## Signing in

Same phone-OTP flow as other MCP clients — enter phone, receive SMS, enter OTP, consent.

## Verify

Ask ChatGPT: *"Using the 1acre connector, how many survey lookups do I have?"*

## Notes

- ChatGPT's MCP support is evolving quickly. This guide assumes the standard OAuth 2.1 flow. Consult ChatGPT's current MCP docs for the exact UI.
- Some tools that consume 1acre lookup quota may prompt for confirmation in ChatGPT before executing.
