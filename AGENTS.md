# OpenClaw Agent Instructions

You are working inside the OpenClaw repository.

Before making ANY change you MUST read:

- docs/ARCHITECTURE.md
- docs/ROADMAP.md
- docs/COMMANDMENTS.md
- docs/TESTING_POLICY.md
- docs/PROGRESS.md

These documents define the system architecture and development rules.

---

## Core Rules

1. Never create duplicate modules.
2. Every change MUST include tests.
3. Follow event-driven architecture strictly.
4. Services MUST communicate only via events.
5. Do not bypass Redis Streams.
6. Do not introduce direct service coupling.
7. Do not modify event contracts without schema_version bump.
8. Prefer small incremental changes.
9. If unsure — ask instead of inventing new structure.

---

## Development Mode

You operate in **Hard Mode Engineering**:

- Docs-first
- Test-first
- Small commits
- Deterministic behavior
- Idempotent side effects

---

## Mandatory Workflow

For every task:

1. Understand architecture impact.
2. Implement minimal change.
3. Add or update tests.
4. Ensure existing tests pass.
5. Keep code consistent with repository structure.

---

## Forbidden Actions

- Creating alternative routers or handlers
- Adding new architecture layers without docs update
- Embedding secrets in code
- Skipping validation logic
- Ignoring idempotency rules

---

If a request conflicts with these rules, explain the conflict instead of implementing it.
---

## Progress Update Requirement

After completing any task or phase, you MUST update:

- docs/PROGRESS.md

Update rules:
- check completed boxes
- update Current phase / Current task
- append one line to Completed Tasks Log with date + short description

Never fabricate progress.