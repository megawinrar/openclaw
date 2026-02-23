# openclaw
> ⚠️ **AI-Driven Development Repository**
>
> This project is developed using strict architecture and engineering rules.
>
> All contributors — including AI agents (Codex) — MUST follow:
>
> - `docs/ARCHITECTURE.md` — system design and service boundaries
> - `docs/ROADMAP.md` — allowed development path and phases
> - `docs/COMMANDMENTS.md` — mandatory engineering rules (Hard Mode)
> - `AGENTS.md` — operational instructions for AI agents
>
> ---
>
> ## Development Rules (Mandatory)
>
> - Services communicate ONLY through events (Redis Streams).
> - No direct service-to-service calls.
> - Every change MUST include tests.
> - No duplicate modules or alternative implementations.
> - Event contracts are stable interfaces.
> - Architecture changes require documentation updates FIRST.
>
> If a change conflicts with these rules, the change must be rejected or redesigned.
>
> ---
>
> ## Engineering Mode
>
> OpenClaw follows **Hard Mode Engineering**:
>
> - Docs-first development
> - Test-first implementation
> - Small incremental commits
> - Deterministic behavior
> - Idempotent side effects
>
> Before implementing any feature, read:
>
> ```
> docs/ARCHITECTURE.md
> docs/ROADMAP.md
> docs/COMMANDMENTS.md
> ```
>
> These documents define the source of truth for the project.
>
> ---
>
> ## For AI Agents (Codex)
>
> AI agents MUST read `AGENTS.md` before making changes.
>
> If unsure about architecture or structure:
>
> **Ask instead of inventing new patterns.**
>
> ---