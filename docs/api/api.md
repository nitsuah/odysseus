# Odysseus API — Setup & CRUD Quickstart

A living reference for wiring up the Odysseus API, testing endpoints, and gradually automating your workspace. Expand each section as you learn more.

---

## 1. Authentication

### Generate a Bearer Token

1. Open Odysseus at `http://localhost:7000`
2. Go to **Settings → API Tokens**
3. Create a new token and select the scopes you need (see scope table below)
4. Copy the `ody_...` token — you won't see it again

### Scope Reference

| Domain | Read | Write | What It Unlocks |
| --- | --- | --- | --- |
| Todos/Tasks | `todos:read` | `todos:write` | list, create, update, toggle |
| Calendar | `calendar:read` | `calendar:write` | list events, create events |
| Notes/Docs | `documents:read` | `documents:write` | read, create, edit documents |
| Memory | `memory:read` | `memory:write` | list, add, delete memories |
| Email | `email:read` | `email:draft` / `email:send` | read inbox, draft, send |
| Cookbook | `cookbook:read` | `cookbook:launch` | manage local model servers |
| Chat | `chat` | — | send messages to sessions |

### Set Your Env Vars

```bash
export ODYSSEUS_URL=http://localhost:7000
export ODYSSEUS_TOKEN=ody_your_token_here
```

Add these to your `.bashrc` / `.zshrc` or a local `.env` file so every snippet below just works.

---

## 2. Verify Everything is Working

### Health check (no auth required)

```bash
curl $ODYSSEUS_URL/api/health
```

### Capabilities check (shows what your token can actually do)

```bash
curl $ODYSSEUS_URL/api/codex/capabilities \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN"
```

This is your best first debug step. If a scope is missing here, go back to Settings and regenerate the token with the right scopes before going further.

---

## 3. API Endpoint Map

All agent-safe endpoints live under `/api/codex/`. Use these for automation — they're scoped, safe, and designed for Bearer token access.

```bash
GET    /api/codex/capabilities          → what this token can do

# Todos / Tasks
GET    /api/codex/todos                 → list all todos
POST   /api/codex/todos                 → create a todo
PATCH  /api/codex/todos/{id}            → update a todo
POST   /api/codex/todos/{id}/toggle     → mark complete/incomplete
DELETE /api/codex/todos/{id}            → delete a todo

# Calendar
GET    /api/codex/calendar/events       → list events
POST   /api/codex/calendar/create_event → create an event

# Documents / Notes
GET    /api/codex/documents             → list documents
GET    /api/codex/documents/{id}        → read a document
POST   /api/codex/documents             → create a document

# Memory
GET    /api/codex/memory                → list memories
POST   /api/codex/memory                → add a memory
DELETE /api/codex/memory/{id}           → delete a memory

# Chat (direct sessions)
POST   /api/chat                        → single-shot message
POST   /api/chat_stream                 → streaming SSE message

# Task Scheduler / Automations
GET    /api/tasks                       → list scheduled tasks
POST   /api/tasks                       → create a scheduled task
DELETE /api/tasks/{id}                  → remove a task

# Webhooks (event triggers)
POST   /api/webhooks/{event}            → fire an event trigger
```

> **Note:** Admin-only routes (like `/api/mcp/servers`) require a cookie session, not a Bearer token. Stick to `/api/codex/*` for all programmatic access.

---

## 4. CREATE Examples

### 4.1 Create a Todo / Task

```bash
curl -X POST $ODYSSEUS_URL/api/codex/todos \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Test task from API",
    "notes": "Created programmatically",
    "due": "2026-06-20T10:00:00"
  }'
```

### 4.2 Create a Calendar Event

```bash
curl -X POST $ODYSSEUS_URL/api/codex/calendar/create_event \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "API test meeting",
    "start": "2026-06-20T14:00:00",
    "end": "2026-06-20T15:00:00",
    "notes": "Created via Odysseus API"
  }'
```

> Calendar uses your local timezone. Odysseus parses natural language dates too — `"tomorrow at 3pm"` works if the endpoint accepts it.

### 4.3 Create a Document / Note

```bash
curl -X POST $ODYSSEUS_URL/api/codex/documents \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "API Notes",
    "content": "# Test\n\nThis note was created via the API."
  }'
```

### 4.4 Add a Memory

```bash
curl -X POST $ODYSSEUS_URL/api/codex/memory \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "User prefers API-first automation over manual UI workflows."
  }'
```

### 4.5 Send a Chat Message

```bash
curl -X POST $ODYSSEUS_URL/api/chat \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Summarize my open todos",
    "model": "llama3",
    "stream": false
  }'
```

---

## 5. Automations (Task Scheduler)

### Create a Recurring LLM Task

Fires an LLM prompt on a schedule and saves output to a session:

```bash
curl -X POST $ODYSSEUS_URL/api/tasks \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Morning briefing",
    "type": "llm",
    "prompt": "Summarize my open todos and any calendar events today. Be concise.",
    "schedule": "daily",
    "time": "08:00",
    "enabled": true
  }'
```

### Create a Cron Task

```bash
curl -X POST $ODYSSEUS_URL/api/tasks \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Weekly review",
    "type": "llm",
    "prompt": "Review my notes and todos from this week. Suggest priorities for next week.",
    "schedule": "cron",
    "cron": "0 9 * * MON",
    "enabled": true
  }'
```

### Supported Schedule Values

| Value | Meaning |
| --- | --- |
| `"once"` | Run immediately, once |
| `"daily"` | Every day at `time` |
| `"weekly"` | Every week at `time` |
| `"monthly"` | First of month at `time` |
| `"cron"` | Full cron expression in `cron` field |
| `"event"` | Fire on an internal event (e.g. `session_created`) |

---

## 6. READ Examples

Once CREATE is confirmed working, layer in these:

```bash
# List todos
curl $ODYSSEUS_URL/api/codex/todos \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN"

# List calendar events
curl "$ODYSSEUS_URL/api/codex/calendar/events?start=2026-06-01&end=2026-06-30" \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN"

# List documents
curl $ODYSSEUS_URL/api/codex/documents \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN"

# List memories
curl $ODYSSEUS_URL/api/codex/memory \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN"

# List scheduled tasks
curl $ODYSSEUS_URL/api/tasks \
  -H "Authorization: Bearer $ODYSSEUS_TOKEN"
```

---

## 7. Debugging Tips

- **403 on a codex route** → your token is missing that scope; regenerate it in Settings
- **403 on a non-codex route** → that route requires a cookie session (admin only); use `/api/codex/*` instead
- **401** → token is invalid or expired
- **Empty capabilities response** → token was created without scopes; delete and recreate
- **Calendar timezone wrong** → Odysseus uses your server's local timezone; check your Docker `TZ` env var
- Always run `GET /api/codex/capabilities` first when something isn't working

---

## 8. Next Steps

- [ ] Confirm all CREATE examples return 200/201 with expected IDs
- [ ] Build out UPDATE (`PATCH`) and DELETE for each resource
- [ ] Write a simple Python or shell script that chains: create task → get ID → update → confirm
- [ ] Set up a webhook trigger from an external source (Home Assistant, cron, etc.)
- [ ] Add an Odysseus Skill in agent chat that calls these endpoints automatically
- [ ] Explore `/api/chat_stream` SSE for live agent output in external tools

---

*Reference: [DeepWiki — Odysseus Codebase](https://deepwiki.com/pewdiepie-archdaemon/odysseus) | [GitHub](https://github.com/pewdiepie-archdaemon/odysseus)*
