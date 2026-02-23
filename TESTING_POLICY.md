# OpenClaw Testing Policy (Test-First Enforcement)

This repository follows **mandatory test-first development**.

All contributors, including AI agents, MUST treat tests as part of the implementation — not as an optional step.

---

## Core Rule

No production code change is complete without tests.

If code changes but tests do not change, the change is considered incomplete.

---

## Required Development Order

Every feature or fix MUST follow this order:

1. Understand architecture impact (see ARCHITECTURE.md)
2. Define expected behavior
3. Write or update tests
4. Implement minimal code required to pass tests
5. Ensure all tests pass
6. Refactor safely (if needed)

---

## Test Coverage Requirements

### libs/

Shared libraries MUST have unit tests covering:

- success path
- failure path
- edge cases
- validation logic

---

### services/

Service logic MUST include:

- handler unit tests
- event validation tests
- idempotency behavior tests

External integrations MUST be mocked.

---

## Event System Testing

Every event handler MUST be tested for:

- valid envelope acceptance
- invalid envelope rejection
- payload validation failure
- idempotent re-processing safety

No handler may skip validation tests.

---

## Command Testing

Commands such as:

```
/api
/status
/tasks
/task
/done
```

MUST have deterministic tests verifying:

- correct output
- invalid input handling
- persistence behavior (Redis mocked)

---

## Forbidden Practices

The following are NOT allowed:

- adding logic without tests
- disabling tests to pass CI
- removing failing tests instead of fixing code
- testing only happy paths
- relying on manual testing instead of automated tests

---

## AI Agent Rules

AI agents MUST:

- create tests together with implementation
- update tests when behavior changes
- refuse large changes without test strategy
- prefer small testable units

If unsure how to test something:
create a minimal testable abstraction first.

---

## Definition of Done

A task is DONE only if:

- behavior implemented
- tests added or updated
- all tests pass
- architecture rules respected
- no duplication introduced

---

## Philosophy

Tests are not verification.

Tests ARE the specification.

Code exists to satisfy tests.