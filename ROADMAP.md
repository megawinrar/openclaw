# OpenClaw Roadmap (Hard Mode)

This roadmap defines the ONLY allowed development path for OpenClaw.

The goal is not fast feature growth, but controlled, testable, production-grade evolution.

Every phase MUST satisfy its Definition of Done (DoD) before moving forward.

---

## Engineering Rules

- One feature = one small commit
- Every change requires tests
- Docs updated before code changes
- Event contracts are stable interfaces
- No duplicate implementations
- Async core + sync workers architecture is fixed

See `docs/COMMANDMENTS.md` for mandatory rules.

---

## Phase 0 — Repository Foundation

Goal: create a stable engineering environment.

### Tasks

- Create repository structure
- Add ARCHITECTURE.md
- Add ROADMAP.md
- Add COMMANDMENTS.md
- Add README.md
- Add `.env.example`
- Configure Python project (pytest)
- Add docker-compose with Redis + Postgres
- Ensure services boot without business logic

### Tests

- Smoke test importing shared libraries
- Environment variables validation test

### Definition of Done

- `docker compose up` runs successfully
- `pytest` runs and passes
- Repository structure matches architecture docs

---

## Phase 1 — Event Backbone

Goal: establish the event-driven foundation.

### Tasks

Implement shared event library:

- event envelope generator
- envelope validation
- payload validation
- trace_id generation
- idempotency_key support
- Redis Streams helpers:
  - publish
  - consume
  - ack
  - create consumer group

### Required Event Fields

- event_id
- event_type
- schema_version
- source
- target
- timestamp
- trace_id
- idempotency_key
- payload

### Tests

- envelope creation test
- missing field validation test
- payload validation test
- deterministic error messages

### Definition of Done

- Events can be produced and validated
- Invalid events are rejected safely
- Tests fully cover envelope logic

---

## Phase 2 — Runtime Control Plane (/api & /status)

Goal: introduce persistent runtime configuration.

### Tasks

Implement single source of truth:

- RuntimeState
- Redis persistence
- `/api`
- `/api <mode>`
- `/status`

Redis key:

```
openclaw:api_mode
```

Valid modes:

```
custom | openai | kimi | auto
```

### Requirements

- Works with sync or async Redis client
- Survives service restart
- No duplicate router implementations

### Tests

- default mode test
- mode persistence test
- invalid mode rejection test
- status formatting test

### Definition of Done

- Mode persists across restarts
- Commands work deterministically
- Tests fully cover command logic

---

## Phase 3 — Async Core MVP (Gateway → Brain → Sender)

Goal: achieve first end-to-end message flow.

### Tasks

#### gateway (async)

- webhook endpoint
- publish `message.received`

#### brain (async)

- consume events
- validate events
- command routing
- echo fallback response
- publish `telegram.send`

#### sender (async)

- consume `telegram.send`
- call Telegram API adapter

### Tests

- event → response transformation test
- command handling tests
- sender handler mock test

### Definition of Done

- Telegram message produces reply
- No direct service-to-service calls
- All communication via events

---

## Phase 4 — Audit & Data Integrity

Goal: prevent silent data loss.

### Tasks

- Implement event_audit table
- Write audit for ALL processing paths
- Guarantee database commit
- Introduce scoped queries

### Critical Rule

Tasks and memory MUST be scoped by chat or user.

### Tests

- audit write test
- audit commit test
- cross-user isolation test

### Definition of Done

- Every processed event appears in audit
- No data leakage between chats

---

## Phase 5 — Scheduler & Idempotent Reminders

Goal: reliable background automation.

### Tasks

- periodic task scanner
- emit reminder events
- deterministic reminder idempotency keys

Example:

```
rem:{task_id}:{time_bucket}
```

### Tests

- overdue detection test
- duplicate prevention test

### Definition of Done

- reminders sent exactly once per window
- retries do not duplicate reminders

---

## Phase 6 — Reliability Hardening

Goal: production stability.

### Tasks

- retry with exponential backoff
- Dead Letter Queue (DLQ)
- poison message detection
- consumer recovery handling

### Tests

- retry simulation
- DLQ routing test
- failure isolation test

### Definition of Done

- bad event cannot crash service
- failed events move to DLQ safely

---

## Phase 7 — Memory Enrichment

Goal: structured long-term context.

### Tasks

- memo_keeper consumes memory events
- tagging pipeline
- optional embeddings (future)

### Tests

- enrichment pipeline test
- memory integrity test

### Definition of Done

- memory enrichment works asynchronously
- no impact on core latency

---

## Phase 8 — Multi-Bot Federation (Future)

Goal: scalable ecosystem.

### Tasks

- specialized bots as event consumers
- arbitration rules for group chats
- cost guardrails

### Definition of Done

- bots deploy independently
- integration ONLY via events

---

## Immediate Fix List (From MVP Audit)

These must be addressed early:

1. Guarantee audit commit
2. Scope tasks by user/chat
3. Fix scheduler UUID casting
4. Rotate exposed secrets
5. Add retry + DLQ

---

## Release Philosophy

OpenClaw evolves through:

- small verified steps
- deterministic behavior
- test-backed progress
- architecture stability

Speed is secondary to correctness.