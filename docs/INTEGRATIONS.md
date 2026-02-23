# Integrations

This document defines how OpenClaw connects external systems and “agent bots” without breaking the core architecture.

OpenClaw is an event-driven platform. Integrations MUST be implemented as independent services (bots/workers) communicating only through Redis Streams events.

---

## 1. Principles

- Core services MUST NOT directly depend on external projects.
- External projects MUST NOT be merged into the core codebase.
- Integrations are isolated and replaceable.
- All communication happens via versioned events with idempotency keys.
- The core orchestrator (brain) remains the decision-maker.

---

## 2. Integration Types

### 2.1 Provider Integrations (AI Router)

Purpose: provide text generation/completions.

Examples:
- custom provider
- OpenAI provider
- Kimi provider

Interface boundary: `AIRequest/AIResponse` (internal), runtime mode via Redis key `openclaw:api_mode`.

---

### 2.2 Agent Bots (External Agents)

Purpose: execute complex reasoning, planning, or domain workflows as a separate service.

Key idea:
- the core brain decides WHEN to call an agent
- the agent processes the request and returns a result
- the core brain decides WHAT to do with the result (reply, tasks, memory, etc.)

Agent bots are consumers/producers on the event bus.

---

## 3. Planned Integration: openclaw/openclaw as a Separate Agent Bot

We plan to integrate the public project `openclaw/openclaw` as an external agent bot, without merging codebases.

Name (placeholder):
- `claw_agent_bot`

Responsibilities:
- consume `agent.requested`
- call the external agent (via HTTP API or CLI adapter)
- publish `agent.responded`

Security boundaries:
- the agent bot MUST NOT access Postgres directly
- the agent bot MUST NOT access Telegram directly (unless explicitly approved)
- the agent bot MUST NOT receive secrets beyond what it needs for its own operation
- the agent bot receives only minimum context required for the task

---

## 4. Event Contracts for Agent Bots

These events are added when we reach the “external agents” phase (after core MVP is stable).

### 4.1 agent.requested

Purpose: request an external agent to process a task.

Envelope:
- standard OpenClaw envelope (see docs/ARCHITECTURE.md)

Minimal payload:

- `request_id` (string)
- `chat_id` (string)
- `user_id` (string, optional)
- `text` (string)
- `context` (object, optional, minimal)

Notes:
- `idempotency_key` SHOULD be stable per request_id
- context MUST be minimal (avoid over-sharing)

---

### 4.2 agent.responded

Purpose: deliver agent output back to the core.

Minimal payload:

- `request_id` (string)
- `status` ("ok" | "error")
- `text` (string, optional)
- `error` (string, optional)
- `metadata` (object, optional)

Notes:
- core brain decides how to apply the response
- response may become:
  - telegram reply (`telegram.send`)
  - task creation (`task.created`)
  - memory update (`memory.added`)

---

## 5. Roadmap Placement

We do NOT integrate external agent bots during Phase 0–3.

Recommended placement:

- After Phase 3 (Async Core MVP stable)
- After Phase 4–6 (audit, reliability, DLQ) if agent calls are mission-critical
- Then add agent contracts + one integration service (`claw_agent_bot`)

---

## 6. Non-Goals (Explicit)

- We do not merge the `openclaw/openclaw` repository into this repository.
- We do not allow direct service-to-service HTTP coupling inside the core.
- We do not allow agent bots to bypass the event bus.
- We do not treat external agents as trusted components.

---

## 7. Definition of Done for an Agent Integration

An agent integration is considered complete when:

- it is isolated as its own service
- it communicates only via events
- it is idempotent and retry-safe
- it has tests for handler behavior
- it respects security boundaries
- it can be disabled without impacting core operation
