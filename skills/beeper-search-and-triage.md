---
name: Search messages and triage a Beeper inbox
description: Search across all connected chats, read message history, and triage conversations (mark read/unread, archive, set reminders) via the local Beeper Desktop API.
api: openapi/beeper-desktop-api-openapi-original.yml
operations: [search, searchMessages, listChats, listMessages, markChatRead, markChatUnread, archiveChat, setChatReminder]
---

# Search messages and triage a Beeper inbox

The Beeper Desktop API keeps all message history local. Base URL `http://localhost:23373`,
`Authorization: Bearer <token>` on every call.

## Steps

1. **Unified search** — call `search` (`GET /v1/search`) for a cross-network query, or
   `searchMessages` (`GET /v1/messages/search`) to scope to message content.
2. **List the inbox** — call `listChats` (`GET /v1/chats`) to page through conversations
   (cursor + direction). For a specific chat, call `listMessages`
   (`GET /v1/chats/{chatID}/messages`) to read recent history.
3. **Triage** — apply actions per chat:
   - `markChatRead` (`POST /v1/chats/{chatID}/read`) / `markChatUnread` (`POST /v1/chats/{chatID}/unread`)
   - `archiveChat` (`POST /v1/chats/{chatID}/archive`)
   - `setChatReminder` (`POST /v1/chats/{chatID}/reminders`) to snooze a chat.

## Rules

- All search/list endpoints are cursor-paginated (`cursor` + `direction`); loop until
  `hasMore` is false. See `conventions/beeper-conventions.yml`.
- Read operations need the `read` scope; triage write actions need `write`.
- For live updates instead of polling, subscribe to the WebSocket event stream
  (`ws://localhost:23373/v1/ws`) described in `asyncapi/beeper-events-asyncapi.yml`.
- Respect `429` backoff and handle the error envelope in `errors/beeper-problem-types.yml`.
