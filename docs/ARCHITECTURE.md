# Architecture Overview

**Status:** Proposed architecture. The current prototype is smaller and does not yet implement every layer described here.

## Design goal

Keep the runtime inspectable and low-overhead. Use deterministic application code to handle policy, permissions, resource limits, and tool execution. Treat model output as a proposal, never as authorization.

## Logical layers

1. **Interface** — CLI first; later web and desktop clients.
2. **Task intake/router** — normalize requests, classify deterministic work, estimate risk and cost.
3. **Resource manager** — inspect available resources and discover local models/backends.
4. **Model scheduler** — serialize calls by default on constrained systems; manage context, output, and model residency.
5. **Agent runtime** — bounded workflow state machine with checkpoints, cancellation, and approval gates.
6. **Tool registry/policy** — validate tool schemas and enforce application-side permissions.
7. **State and memory** — separate task state, session history, audit events, approved memory, and reusable workflows.
8. **Observability** — provide local diagnostics without recording secrets or private document contents by default.

## Model and tool boundary

The model may suggest a tool and its arguments. The application must independently confirm that the tool exists, validate its input schema, verify the user's permission and the tool's allowed scope, enforce file/output/time/resource limits, request approval where required, and record an auditable outcome.

Prompt instructions alone are not a security boundary.

## Low-resource execution

Initial operating assumptions: one active model call at a time unless measurements justify more; deterministic operations do not invoke a model unnecessarily; context and output are bounded; model unloading/keep-alive behavior is configurable; functionality degrades gracefully when local inference is unavailable.

## Persistence

SQLite is the initial persistence direction. Task state, session history, audit data, and durable user-approved memory should have separate responsibilities and retention controls.

## Current implementation boundary

The working prototype includes a CLI, Ollama inference, SQLite history/audit, a calculator, and restricted workspace file reading. The proposed resource manager, durable workflow state machine, richer tool capabilities, and additional interfaces remain roadmap items.
