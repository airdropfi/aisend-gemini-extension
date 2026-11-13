# AISend for Gemini CLI

> AISend is a paid transactional email API built for AI agents to operate and humans to supervise: an agent sends from your verified domain, checks delivery and validates addresses over MCP or a Resend-compatible API, while you approve, review and can revoke access.

## AISend at a glance

- **What it is:** AISend is a paid transactional email API built for AI agents to operate and humans to supervise: an agent sends from your verified domain, checks delivery and validates addresses over MCP or a Resend-compatible API, while you approve, review and can revoke access.
- **Primary user:** An AI agent (ChatGPT, Claude, Cursor, Gemini CLI or any MCP client) operates it: sends email, checks delivery, validates addresses. A human tags the work, approves the connection, reviews what the agent did and can revoke access.
- **Who pays for it:** Product and engineering teams at SaaS companies and agencies that send password resets, receipts and notifications from their own domains, and individuals or small businesses who want an assistant to send and track email from a domain they own.
- **What it does not do:** Cold outreach, purchased lists, bulk marketing or newsletters. No campaigns or automations. No free plan and no free sending: an account needs an active paid plan before it can send. Sending to anyone other than your own account address needs a domain you have verified.
- **Price:** From $14/month (Pro, 50,000 emails/month), billed monthly per account. Business $63/month for 100,000; Scale $149/month for 500,000. No per-email overage charges. No free plan.
- **Integrations:** MCP (hosted https://api.aisend.app/mcp and local npx aisend-mcp), Resend-compatible REST API, Node.js SDK (aisend-email), HMAC-SHA256 signed webhooks, delivery through AWS SES.
- **Security and data:** Sends through AWS SES. Data encrypted in transit and at rest; API keys stored hashed. OAuth 2.1 with PKCE for assistants; the key an OAuth sign-in creates works only on the MCP endpoint, expires after 90 days, is limited to 100 emails a day and can be revoked on the API Keys page.
- **Start in minutes:** Create an account (free), add a domain you own and publish its DNS records (propagation time varies), subscribe to a plan, then connect an assistant at https://aisend.app/mcp (about a minute) or create an API key.
- **MCP:** https://api.aisend.app/mcp (Streamable HTTP, OAuth or API key). Official MCP Registry name: app.aisend/aisend.

## Connect in 60 seconds

```bash
gemini extensions install https://github.com/airdropfi/aisend-gemini-extension
```

Restart Gemini CLI, run `/mcp auth aisend`, sign in to AISend and press Approve. You need an AISend account with an active plan and, to send to anyone but yourself, a verified sending domain.

## Example prompts

You tag the work; the agent calls the tools; you review and approve.

- `Assign to AISend: email billing@client.example the approved invoice reminder from our verified domain, idempotency key invoice-2026-11-reminder. Show me the exact message first and send only after I say yes.`
- `Use AISend to list my verified sending domains and tell me which one to send from. Do not send anything.`
- `Use AISend to show the status of my last 10 emails and flag any that bounced or complained, so I can review them.`
- `Use AISend to validate these addresses and list the disposable or role-based ones. Show me the list so I can decide.`

## Human oversight: what you can see, approve and revoke

- **Approve or refuse a connection.** The OAuth consent screen names the client, the account and the redirect host, lists what the assistant will be able to do (send from verified domains under normal limits, read sent emails and delivery status, validate addresses, list domains and API key names) and has Approve and Deny buttons.
- **Approve each send, in clients that ask.** send_email is annotated destructive and not read-only, and the reads are annotated read-only, so clients that confirm tool calls ask before a send. That prompt is the client's setting; AISend does not enforce it.
- **Revoke access.** Every OAuth connection is an API key on the API Keys page. Revoking it makes the next call fail with 401. Connections also expire after 90 days.
- **See what was created.** API Keys page: key names, prefixes and revoked state (the assistant's connection appears as "MCP OAuth: <client>"). Emails page: every sent email with its delivery status.
- **Bound the blast radius.** An assistant connection is capped at 100 emails a day. Plan quotas, the daily cap, new-account ramp-up, new-domain caps, the verified-domain requirement, suppression of bounced and complained addresses and the content gate apply to every send, from an agent or not.
- **Watch events as they happen.** Webhooks deliver sent, delivered, opened, clicked, bounced and complained events, signed with HMAC-SHA256, to an endpoint the human controls.

## Links

- Product: https://aisend.app
- MCP page with the agent quickstart (markdown: https://aisend.app/mcp.md): https://aisend.app/mcp
- Pricing: https://aisend.app/pricing
- Official MCP Registry name: `app.aisend/aisend`
- MCP server card: https://aisend.app/.well-known/mcp/server-card.json
- llms.txt: https://aisend.app/llms.txt
- Other ways in: [Claude Code plugin](https://github.com/airdropfi/aisend-claude-plugin), [Cursor kit](https://github.com/airdropfi/aisend-cursor-kit), [Gemini CLI extension](https://github.com/airdropfi/aisend-gemini-extension), npm [`aisend-mcp`](https://www.npmjs.com/package/aisend-mcp)

## Details

Send email from your own verified domain with [AISend](https://aisend.app) without leaving Gemini CLI. The extension connects Gemini to the AISend MCP server and adds four slash commands and a context file that keep you in control: the agent shows you every message first, and nothing is sent until you say yes.

## Before you start

- **You need an AISend account** (sign up at https://aisend.app). The extension is a connector to your own account, not a mail service of its own.
- **Sending is real.** `send_email` delivers mail immediately and it cannot be recalled. Your account's sending limits and domain checks apply unchanged.
- **Add a domain first.** To send to other people you verify a sending domain at https://aisend.app/domains. Until then you can only send test mail from an address at `send.aisend.app` to your own account email.
- You need Gemini CLI. A browser must be available once, for sign-in.

## Install

```bash
gemini extensions install https://github.com/airdropfi/aisend-gemini-extension
```

Restart Gemini CLI, then sign in:

```text
/mcp auth aisend
```

A browser window opens: sign in to AISend and approve the connection. Sign-in is OAuth 2.1 with PKCE, discovered from the server, so there is no key to paste. Check it with `/mcp` (the `aisend` server should list its tools) and try `/aisend:domains`.

Update later with `gemini extensions update aisend`; remove with `gemini extensions uninstall aisend`.

### What the sign-in creates

Approving the connection gives Gemini an API key named after the client (for example `MCP OAuth: ...`). That key works only on the AISend MCP endpoint, expires after 90 days (you then reconnect), has a daily send cap (100 sends a day by default on the hosted service) and can be revoked any time on the API Keys page at https://aisend.app/api-keys.

### Antigravity CLI

This repository is also laid out as an Antigravity plugin (`.antigravity-plugin/plugin.json`, `mcp_config.json`, `rules/AGENTS.md`) that connects the same MCP server. The slash commands below are Gemini CLI commands; in Antigravity you describe what you want. We have not run the plugin end to end in Antigravity.

## Commands

| Command | What it does |
|---|---|
| `/aisend:send <what to send, to whom>` | Picks a verified sender, shows you the full message, sends only after your yes, then checks status. |
| `/aisend:status [filter]` | Recent emails with delivery status. Read-only. |
| `/aisend:validate <address>` | Checks format, MX, disposable and role addresses. Sends nothing; counts against your monthly validation quota. |
| `/aisend:domains` | Your sending domains and which are verified. Read-only. |

You can also just describe what you want ("email the team that the deploy finished"); `GEMINI.md` carries the same rules.

## Tools

| Tool | Notes |
|---|---|
| `send_email` | Destructive and real. Needs a new `idempotency_key` per email; reuse it when retrying. Up to 50 recipients per call. |
| `get_email_status` | Read-only. One email by id. |
| `list_emails` | Read-only. Newest first, filter by status or search. |
| `validate_email` | Metered against the monthly validation quota. |
| `list_domains` | Read-only. |
| `list_api_keys` | Read-only. Names and prefixes only. |

The hosted server cannot create API keys or accounts.

## What it will and will not do

- It shows the whole message and waits for a clear yes before every send.
- It sends only from a domain you verified, and refuses bulk mail to purchased lists and deceptive or unsolicited mail.
- It reports delivery status exactly as AISend returns it and never claims delivery that the status does not show.
- It never asks you to paste an API key into the chat.

## Data and privacy

The MCP server receives what the tools need: the messages and addresses you ask the agent to send or look up, and the email, domain and key-name data on your AISend account. This repository stores nothing and runs no code on your machine; it is a server address, Markdown instructions and a logo. See the [privacy policy](https://aisend.app/privacy) and [terms](https://aisend.app/terms).

## Layout

```text
gemini-extension.json        extension manifest (MCP server aisend)
GEMINI.md                    always-on rules
commands/aisend/*.toml       slash commands
mcp_config.json              MCP server for Antigravity
rules/AGENTS.md              rules for Antigravity
.antigravity-plugin/         Antigravity plugin manifest
scripts/validate.mjs         offline validator (node scripts/validate.mjs)
assets/icon.png              1024x1024 icon
```

## Support

https://aisend.app/docs or hello@aisend.app.

## License

MIT for the files in this repository (see `LICENSE`). The AISend service is governed by its terms.
