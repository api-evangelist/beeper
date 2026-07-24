---
name: Send a message in a Beeper chat
description: Find or start a chat across a user's connected messaging networks and send a message into it using the local Beeper Desktop API.
api: openapi/beeper-desktop-api-openapi-original.yml
operations: [getAccounts, searchChats, startChat, sendMessage, getChat]
---

# Send a message in a Beeper chat

Beeper's Desktop API runs locally at `http://localhost:23373`. All calls require an
`Authorization: Bearer <token>` header (token from the OAuth2 PKCE flow or created in-app).

## Steps

1. **List connected accounts** — call `getAccounts` (`GET /v1/accounts`) to see which
   messaging networks are bridged (WhatsApp, Signal, Telegram, etc.) and get their `accountID`s.
2. **Locate the chat** — call `searchChats` (`GET /v1/chats/search`) with a query to find an
   existing conversation, or `startChat` (`POST /v1/chats/start`) / `createChat`
   (`POST /v1/chats`) to begin a new one. Capture the `chatID`.
3. **(Optional) confirm the chat** — call `getChat` (`GET /v1/chats/{chatID}`) to verify the
   participants and capabilities before writing.
4. **Send** — call `sendMessage` (`POST /v1/chats/{chatID}/messages`) with the message text
   (and optional attachments). Requires the `write` scope.

## Rules

- Pagination on search/list is cursor-based: pass the opaque `cursor` plus `direction`; never
  parse the cursor. See `conventions/beeper-conventions.yml`.
- There is no idempotency key — do not blindly retry a `sendMessage` that may have already
  succeeded; re-query messages first.
- Handle `401` (bad/expired token), `403` (missing `write` scope), `404` (chat not found),
  and `429` (rate limited) per `errors/beeper-problem-types.yml`.
