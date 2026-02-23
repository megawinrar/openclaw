# OpenClaw Architecture

## 1. Overview

OpenClaw is an event-driven Telegram automation system built around independent services communicating through Redis Streams.

The system receives Telegram messages via webhook, processes them through a central orchestrator ("brain"), and produces replies, tasks, and memory events using a strict event contract.

Design goals:

- Event-driven communication
- Loose coupling between services
- Idempotent processing
- Async I/O where required
- Production-ready observability
- Docs-first and test-first development

---

## 2. High-Level Topology

```
Telegram
   ↓
gateway (async webhook ingress)
   ↓ message.received
Redis Streams (event bus)
   ↓
brain (async orchestrator)
   ├── telegram.send → sender
   ├── task.created → scheduler
   └── memory.added → memo_keeper

sender (async) → Telegram API
scheduler (sync worker)
memo_keeper (sync worker)
telemetry (sync worker)
```

Rule: services NEVER call each other directly.  
All interaction happens strictly through events.

---

## 3. Service Types

### Async Core (I/O heavy)

| Service | Responsibility |
|---|---|
| gateway | Telegram webhook ingress |
| brain | orchestration & command handling |
| sender | Telegram message delivery |

These services MUST be asynchronous.

---

### Sync Workers (background processing)

| Service | Responsibility |
|---|---|
| scheduler | deadline reminders |
| memo_keeper | memory enrichment |
| telemetry | metrics and audit aggregation |

Workers remain synchronous for simplicity and stability.

---

## 4. Services

### 4.1 gateway

Role: Telegram ingress.

Responsibilities:
- receive webhook updates
- normalize incoming messages
- publish `message.received` events

Idempotency key format:

```
tg:{chat_id}:{message_id}
```

---

### 4.2 brain (Orchestrator)

Central decision engine.

Consumes:
- `message.received`

Produces:
- `telegram.send`
- `task.created`
- `memory.added`

Command priority:

1. `/api`, `/status`
2. task & memory commands
3. normal message flow

Brain MUST NOT call Telegram API directly.

---

### 4.3 sender

Consumes `telegram.send`.

Responsibilities:
- validate event
- send message to Telegram
- ensure idempotent delivery

---

### 4.4 scheduler

Periodic worker.

Responsibilities:
- detect overdue tasks
- emit reminder events

Reminder idempotency example:

```
rem:{task_id}:{time_bucket}
```

---

### 4.5 memo_keeper

Consumes memory events and enriches stored context.

Future responsibilities may include tagging or embeddings.

---

### 4.6 telemetry

Collects:
- metrics
- structured logs
- audit data

Telemetry must never block core processing.

---

## 5. Event System

### 5.1 Mandatory Event Envelope

Every event MUST contain:

```json
{
  "event_id": "string",
  "event_type": "string",
  "schema_version": "1.0.0",
  "source": "string",
  "target": "string",
  "timestamp": "ISO-8601",
  "trace_id": "string",
  "idempotency_key": "string",
  "payload": {}
}
```

---

### 5.2 Event Types

#### message.received

Payload fields:

- chat_id
- message_id
- text

---

#### telegram.send

Payload fields:

- chat_id
- text

---

#### memory.added

Payload fields:

- memory_id
- user_id
- content

---

#### task.created

Payload fields:

- task_id
- title
- priority

---

### 5.3 Validation Rules

Each consumer MUST:

1. validate envelope fields
2. validate event_type
3. validate minimal payload schema

Invalid events:
- MUST NOT be processed
- MUST be logged with reject reason

---

## 6. AI Provider Mode (Redis State)

API mode is persisted in Redis.

Redis key:

```
openclaw:api_mode
```

Valid modes:

```
custom | openai | kimi | auto
```

Commands:

```
/api
/api <mode>
/status
```

`/status` exposes runtime diagnostics:

- active provider
- fallback count
- last provider error

There MUST be exactly ONE implementation of API mode logic.

---

## 7. Data Storage

PostgreSQL stores:

### tasks
- task_id
- chat/user scope
- title
- due_at
- status
- timestamps

### memory
- memory_id
- user_id
- content
- tags
- timestamps

### event_audit
- event metadata
- trace_id
- processing result

Audit writes MUST commit on ALL execution paths.

---

## 8. Delivery Semantics

Redis Streams provide AT-LEAST-ONCE delivery.

Therefore:

- consumers MUST be idempotent
- side effects must tolerate retries
- idempotency_key is REQUIRED for all side effects

---

## 9. Reliability Rules

- retry transient errors with backoff
- move poison events to DLQ stream
- never crash service due to a single bad event

---

## 10. Secrets Policy

Secrets MUST NOT be stored in:

- repository
- documentation
- chat history

Use:

- `.env`
- secret manager (production)

Leaked keys MUST be rotated immediately.

---

## 11. Development Model

OpenClaw follows Hard Mode engineering:

- Docs-first
- Test-first
- Small commits
- No duplicate logic
- Events are contracts

See:

```
docs/COMMANDMENTS.md
docs/ROADMAP.md
```