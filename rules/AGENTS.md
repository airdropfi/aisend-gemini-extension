# AISend for Gemini CLI

AISend (https://aisend.app) sends email from the user's own verified domain and tracks what happened to it. This extension connects Gemini CLI to the user's own AISend account through the `aisend` MCP server (`https://api.aisend.app/mcp`). The tools are `send_email`, `get_email_status`, `list_emails`, `validate_email`, `list_domains` and `list_api_keys`.

If the AISend tools are not available or answer that sign-in is required, tell the user to run `/mcp auth aisend` and finish the browser sign-in. You cannot sign in for them. The user needs an AISend account.

## Rules that always apply

1. **Sending is real.** `send_email` delivers real mail right away and it cannot be recalled. Before every send, show the user the full message (from, to, subject, body) and wait for a clear yes to that exact message. One approval covers one email: a yes to an earlier draft is not approval for a changed one, and never send "just to test" without asking.
2. **Idempotency key.** `send_email` requires `idempotency_key`. Use a new unique value (for example a UUID) for each new email. If a send times out or errors, retry with the SAME key so the recipient never gets two copies.
3. **Verified domains only.** `from` must use a domain the user verified in AISend. If you are not sure, call `list_domains` first and tell the user which domains are verified. Sending needs an active paid plan. Until a domain is verified, a paid account can only send from an address at `send.aisend.app` to the user's own AISend account email. Domains are added and verified by the user at https://aisend.app/domains; you cannot do it for them.
4. **Mail people expect.** One call sends to at most 50 recipients. Do not send to purchased or scraped lists, and do not write deceptive, phishing, impersonating or unsolicited bulk mail. Refuse and explain.
5. **Validation is metered.** `validate_email` checks format, MX records, disposable providers and role addresses, and each call counts against the account's monthly validation quota. Use it when the user asks or for a handful of addresses, not for long lists.
6. **Report status as returned.** `get_email_status` and `list_emails` give the delivery status (queued, sending, sent, delivered, failed, bounced, complained) and opened/clicked flags. Say exactly what the tool returned; never claim an email was delivered unless the status says so.
7. **Sending limits.** An assistant sign-in (OAuth) gets its own API key that works only on the AISend MCP endpoint, expires after 90 days and has a daily send cap (100 sends a day by default). If a send is refused because of a limit, tell the user in plain words and do not try to work around it.
8. **Keys stay out of chat.** This server cannot create API keys or accounts. `list_api_keys` shows names and prefixes only. If the user needs a key, point them to https://aisend.app/api-keys and never ask them to paste a key into the conversation.

## Commands

- `/aisend:send <what to send, to whom>`: draft, show, send only after a clear yes, then check status.
- `/aisend:status [filter]`: recent emails and their delivery status (read-only).
- `/aisend:validate <address>`: validate an address before sending (uses validation quota; sends nothing).
- `/aisend:domains`: which sending domains are verified (read-only).

If the user asks for something in these areas without a command, follow the same rules.
