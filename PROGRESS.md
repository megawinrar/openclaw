# OpenClaw Progress Tracker

This file is the single source of truth for the current project progress.

AI agents MUST:
- read this file before starting work
- update this file after completing a task/phase
- keep updates minimal and factual

---

## Current Status

- Current phase: Phase 0 — Repository Foundation
- Current task: Not started

---

## Phase Checklist

### Phase 0 — Repository Foundation
- [ ] Repository structure created (docs/, libs/, services/, tests/)
- [ ] README.md added
- [ ] .env.example added
- [ ] docker-compose.yml added (Redis + Postgres + service stubs)
- [ ] Python project configured (pytest)
- [ ] Smoke tests added and passing

### Phase 1 — Event Backbone
- [ ] libs/eventkit/envelope.py implemented + tests
- [ ] libs/eventkit/validate.py implemented + tests
- [ ] libs/eventkit/streams.py implemented + tests
- [ ] Event contract validation used by at least one service handler

### Phase 2 — Runtime Control Plane (/api, /status)
- [ ] libs/runtime/api_mode.py implemented + tests
- [ ] Redis persistence confirmed (mocked) + tests
- [ ] /status output stable + tests

### Phase 3 — Async Core MVP (Gateway → Brain → Sender)
- [ ] gateway publishes message.received (webhook)
- [ ] brain consumes message.received and publishes telegram.send
- [ ] sender consumes telegram.send and calls Telegram adapter
- [ ] End-to-end path works (smoke)
- [ ] Handler unit tests exist

### Phase 4 — Audit & Data Integrity
- [ ] event_audit table + writes on all paths
- [ ] audit commit guaranteed
- [ ] tasks scoped by chat/user + tests

### Phase 5 — Scheduler & Idempotent Reminders
- [ ] scheduler scans overdue tasks
- [ ] reminders published as telegram.send
- [ ] reminder idempotency enforced + tests

### Phase 6 — Reliability Hardening
- [ ] retry with backoff
- [ ] DLQ stream
- [ ] poison message handling + tests

### Phase 7 — Memory Enrichment
- [ ] memo_keeper enrich pipeline + tests
- [ ] optional embeddings plan documented

### Phase 8 — Multi-Bot Federation
- [ ] federation plan documented
- [ ] arbitration rules documented

---

## Completed Tasks Log

(append-only, newest first)

- (none yet)