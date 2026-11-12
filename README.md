# AISend for Gemini CLI

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
