# Low-Resource Agent — Project Plan

**Status:** Strategic plan and roadmap. This document describes intended direction; it does not imply that planned functionality is implemented.

## 1. Vision

Make practical AI-assisted productivity accessible to people whose computers cannot comfortably run large language models.

## 2. Mission

Build a trustworthy, resource-aware agent that works locally by default, uses small models efficiently, and gives users understandable control over what it can read, change, send, or execute.

## 3. Product principles

1. **Local-first, cloud-optional.** Core functions should remain useful without a cloud account. Cloud routing must be explicit, visible, configurable, and opt-in.
2. **CPU is a first-class target.** GPU acceleration is an optional optimization, not a requirement.
3. **Deterministic before generative.** Use ordinary code for arithmetic, validation, file operations, data transforms, scheduling, and policy enforcement.
4. **The model proposes; the application authorizes.** Model output is untrusted input. Application code owns permissions and execution.
5. **One core, multiple interfaces.** Start with a CLI; later interfaces should call the same engine.
6. **Resource-aware by design.** Measure available RAM, CPU, GPU capability, model size, context, concurrency, and latency before selecting a runtime configuration.
7. **Graceful degradation.** When inference is too slow or memory is low, deterministic tools and simpler workflows should remain available.
8. **Inspectable and recoverable.** Make plans, tool calls, approvals, results, errors, and task state visible. Design for retry, cancellation, and resume.
9. **Privacy and user control.** Keep local data local unless the user explicitly approves external transfer. Provide retention and deletion controls.
10. **Honest capability claims.** Publish benchmark conditions and limitations; do not imply that small models generally match larger models.

## 4. Current prototype baseline

The working prototype developed in the project is the Hybrid AI Agent series, a Windows/Python CLI using Ollama for local inference and optional Gemini integration for explicitly approved complex requests.

Known baseline from development notes:

- Python 3.12 environment.
- Ollama server at `http://localhost:11434`.
- Tool-selection model: `lfm3:1.7b`.
- Local response model: `qwen3:1.7b`.
- Optional remote model setting: `gemini-3.8-flash`; availability and SDK compatibility must be checked at release time.
- SQLite task history and tool audit.
- Calculator and restricted workspace file reader.
- File reader restricted to selected text-oriented extensions and a project workspace.
- Application-level approval for supported tool actions.
- Path and input checks exist in the prototype, but the design is not an OS-level sandbox.

Initial developer hardware used for baseline work:

- Windows 10 Pro 64-bit.
- Intel Core i5-6200U, 2 cores / 4 logical processors.
- Approximately 8 GB RAM.
- GeForce 920M with 2 GB DDR3 VRAM and an old driver; the initial target strategy is CPU inference.

### Preliminary model comparison

A one-run comparison was recorded for three tool-selection models, with three tasks per model:

| Model | Tasks passed | Recorded elapsed time |
|---|---:|---:|
| DeepSeek R1 1.5B | 1/3 | 118.19 seconds |
| Qwen3 1.7B | 3/3 | 82.17 seconds |
| LFM3 1.7B | 3/3 | 55.69 seconds |

This is a small preliminary observation, not a statistically robust benchmark and not a general ranking across tasks or hardware.

## 5. Architecture direction

### User interface

- CLI first.
- Later local web interface or desktop shell.
- Interfaces should call a shared engine and permission system.

### Task intake and deterministic router

- Validate and normalize requests.
- Identify deterministic tasks that do not require a model.
- Estimate task risk and resource cost.
- Ask for clarification or user approval where required.
- Never rely solely on an LLM to authorize actions.

### Resource manager and model scheduler

- Detect available memory and hardware characteristics.
- Discover inference backends and installed models.
- Maintain model metadata and benchmark observations.
- Select a model using task needs and measured resources.
- Serialize inference on low-resource systems by default.
- Manage context, output, timeout, retry, and model-residency budgets.
- Expose optional cloud routing only with explicit consent.

Initial backend direction: Ollama first; llama.cpp / llama-server as a later alternative; optional OpenAI-compatible endpoint adapter after the core stabilizes.

### Agent runtime

Represent tasks as explicit states, for example:

`CREATED -> PLANNED -> AWAITING_APPROVAL -> RUNNING -> WAITING_FOR_MODEL -> COMPLETED / FAILED / CANCELLED`

Longer-term requirements:
- bounded number of steps and retries;
- timeouts and cancellation;
- checkpoints after completed steps;
- recovery after process restart;
- approval gates before sensitive actions;
- visible budgets for autonomous work.

### Tool registry and policy enforcement

Each tool should declare its name/version, input and output schemas, required capabilities, allowed resources, risk level, approval policy, size limits, timeout, and audit metadata.

Initial tool roadmap: calculator; workspace file reader; workspace file creation/writing with overwrite confirmation; CSV/JSON processing; document extraction and report creation; notes and task lists; browser research with source capture; communication drafting with explicit approval before sending; user-defined scheduled workflows.

Shell and arbitrary code execution should remain disabled by default. If introduced, they require a real isolation boundary and a reviewed threat model.

### Local state and memory

Separate current task state, short-term conversation history, tool audit events, user-approved durable memory, and reusable skills/workflow definitions.

SQLite is the initial persistence choice. Future work includes migrations, configurable retention, export/delete controls, and encryption-at-rest guidance. Do not turn all conversation content into durable memory automatically.

### Observability

Measure and report locally where practical: model load time and inference latency; tokens/second where available; process/system memory; selected backend/model and routing reason; context/input/output sizes; tool calls, approvals, errors, retries, and cancellation; cloud usage and external data transmission.

Never log API keys, passwords, raw environment variables, or sensitive document content by default.

## 6. Resource-efficiency strategy

1. Avoid LLM calls for deterministic work.
2. Use one active model by default on entry-level hardware.
3. Adapt context to task needs and available memory.
4. Chunk longer documents and preserve references during synthesis.
5. Recommend quantized models only after measuring memory, quality, and latency.
6. Bound generation length and stop conditions.
7. Limit steps, retries, duration, file size, output size, and network calls.
8. Cache only safe reusable results.
9. Avoid heavy concurrent inference on low-memory systems.
10. Preserve deterministic functionality when a model cannot load.

### Planning hardware tiers

These are initial planning bands, not verified compatibility promises.

| Tier | Installed RAM | Intended behavior |
|---|---:|---|
| Minimal | 4 GB or less | Deterministic tools first; local model only after measured fit |
| Entry | 8 GB | Small quantized model, CPU-first, one active model, short context |
| Standard | 16 GB | More model choices and longer workflows after benchmarking |
| Advanced | 32 GB+ | Larger local models and optional concurrency after benchmarking |

Recommendations must account for available memory, model weights, KV cache/context, runtime overhead, and concurrent applications.

## 7. Security and privacy

Treat user prompts, file contents, retrieved pages, tool outputs, model outputs, third-party skills, and downloaded artifacts as untrusted until validated.

Mandatory design goals: default-deny tool registry; least-privilege capabilities; canonical path checks and symlink-aware workspace validation; explicit write/overwrite and send/share approval; no secrets in model context or logs; strict input/output schemas; bounded file, context, output, and execution sizes; audit records for allow, deny, failure, and success; adversarial prompt-injection tests; dependency and model provenance review; user-controlled history retention and deletion.

Test areas include traversal, absolute paths, symlink/junction escapes, hidden files, Unicode filename handling, prompt injection, malicious tool outputs, unauthorized tool calls, oversized or malformed files, calculator limits, secret leakage, approval bypass, cancellation, and resource exhaustion.

A passing test suite is evidence for tested cases only—not proof of complete security.

## 8. Roadmap and release gates

### v0.3.2 — Security and reliability hardening

- UTF-8 BOM handling.
- Reusable tool registry and argument validation.
- Calculator AST and resource bounds.
- File and output size limits.
- SQLite privacy and retention controls.
- Structured audit outcomes.
- Automated regression tests.
- Preserve the existing CLI and routing behavior.

**Release gate:** functional tests pass on the supported environment; security tests cover the current tool surface; baseline workflows show no regression.

### v0.4 — Resource-aware engine

- Hardware and available-memory detection.
- Backend and installed-model discovery.
- Model registry and recommendations.
- Context/output/concurrency budgets.
- Single-model scheduler and keep-alive policy.
- Ollama adapter first; compare llama.cpp after benchmarking.

**Release gate:** publish reproducible CPU-focused results on 8 GB hardware and at least one other tier.

### v0.5 — Stateful task planning

- Task plan schema and deterministic state machine.
- Step budgets, checkpoints, retries, cancellation, and resume.
- Approval checkpoints and execution trace.

**Release gate:** multi-step workflows recover correctly after errors and process restart.

### v0.6 — Productivity modules

- Documents and files.
- CSV/spreadsheet analysis.
- Notes and task lists.
- Research with citations/source capture.
- Communication drafting.
- Scheduled workflows.

**Release gate:** each module has documented permissions, tests, and measured resource costs.

### v0.7 — User experience

- Local web UI or desktop shell.
- Guided setup and hardware profile.
- Model management and benchmark viewer.
- Task history, approvals, diagnostics, and skills gallery.

**Release gate:** beginner installation test on clean Windows and Linux systems.

### v1.0 — Stable release

- Stable core interfaces and configuration.
- Migration and backup strategy.
- Documented security boundaries.
- Reproducible benchmark suite.
- Installation and update process.
- Contribution and release processes.

## 9. Research and competitive context

The project plan reviewed existing work in OpenJarvis, eVi, Nanobot, Hugging Face smolagents, LangGraph, Ollama, llama.cpp, and the OWASP GenAI Security Project. The project should learn from established work and avoid unsupported claims such as being the first local, CPU-based, or hardware-aware agent.

## 10. Benchmark publication standard

Every benchmark report should include machine hardware and OS; model name/tag/revision and quantization where known; inference backend/runtime version; context and generation settings; warm/cold model state; latency and throughput; peak RAM and VRAM where measured; task success criteria, failures, and sample count; and whether the test is exploratory or statistically meaningful.

## 11. Current non-goals

Until the architecture and security model mature: no unrestricted shell or arbitrary Python execution; no autonomous external sending without approval; no cloud inference as a mandatory dependency; no claim of comprehensive security; no claim that a particular model works well on every low-memory machine; no claim of cross-platform support based only on Windows testing.
