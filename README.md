# Low-Resource Agent

**A local-first AI productivity agent designed for older computers and resource-constrained systems.**

[GitHub Repository](https://github.com/ashiquebiniqbal/ashiquebiniqbal-low-resource-agent) · Apache License 2.0 · Experimental Development

---

## 1. Project Overview

Low-Resource Agent is an open-source project exploring how to make practical AI-assisted productivity accessible to people who cannot comfortably run large language models on their computers.

The project aims to combine small local language models, deterministic software tools, resource-aware execution, and user-controlled automation into a lightweight productivity agent.

The goal is not to make a small model imitate a frontier model at every task. Instead, the goal is to build useful workflows around the actual capabilities and limitations of modest hardware.

### Our Vision

**Useful AI should not require expensive hardware.**

We want to make local-first AI more practical for students, independent professionals, developers, and everyday computer users, particularly those using older laptops, CPU-only systems, or computers with limited RAM.

### Core Objectives

- Run useful AI-assisted workflows on older computers.
- Make CPU inference a first-class development target.
- Use small and quantized local models efficiently.
- Prefer deterministic code when an LLM is unnecessary.
- Keep model execution, tool permissions, and application logic separate.
- Give users control over file access, external communication, and automation.
- Make cloud inference optional rather than mandatory.
- Publish reproducible benchmarks and honest capability limitations.

---

## 2. Why This Project?

Running local AI can be challenging for people with limited hardware resources.

Large models require substantial memory and processing capacity. Even smaller models can become slow or impractical when several models, large contexts, or concurrent requests compete for system resources.

Many agent frameworks also introduce additional dependencies and infrastructure that may not suit older computers.

Low-Resource Agent explores a different approach:

| Challenge | Project direction |
|---|---|
| Limited RAM | Small models, bounded context, and controlled model residency |
| No powerful GPU | CPU-first inference |
| Slow inference | Deterministic tools and fewer unnecessary model calls |
| Small models struggle with complex tasks | Structured workflows and optional approved cloud assistance |
| Excessive agent permissions | Narrow tools and application-side authorization |
| Privacy concerns | Local-first operation and explicit external-transfer approval |
| Complex installation | Beginner-oriented setup and minimal dependencies |

These are engineering objectives to validate through implementation and benchmarking, not claims that the current prototype has already solved every problem.

---

## 3. Current Development Status

**Project stage: Experimental prototype and foundation development.**

The initial implementation is part of the Hybrid AI Agent series, developed as a Python-based command-line application using Ollama for local inference.

The repository is being established incrementally. The prototype source code and tests are maintained separately during local validation before publication.

### Existing Prototype Capabilities

The development baseline includes:

- Python-based command-line interface.
- Ollama integration for local model inference.
- Optional Gemini integration for approved remote requests.
- SQLite-based task history and tool auditing.
- Restricted workspace file reading.
- Calculator tool using Python AST validation.
- User approval for supported tool operations.
- Configurable local model selection.
- Application-level input and file validation.

### Development Environment

The initial development and testing environment:

| Component | Configuration |
|---|---|
| Operating system | Windows 10 Pro 64-bit |
| CPU | Intel Core i5-6200U |
| CPU configuration | 2 cores / 4 logical processors |
| RAM | 8 GB |
| GPU | NVIDIA GeForce 920M, 2 GB DDR3 |
| Python | 3.12 |
| Local inference | Ollama |
| Database | SQLite |

The initial target is CPU inference rather than depending on modern GPU hardware.

### Initial Model Experiments

A preliminary one-run comparison was conducted using three tool-selection tasks per model.

| Model | Tasks passed | Recorded elapsed time |
|---|---:|---:|
| DeepSeek R1 1.5B | 1/3 | 118.19 seconds |
| Qwen3 1.7B | 3/3 | 82.17 seconds |
| LFM3 1.7B | 3/3 | 55.69 seconds |

These observations are specific to the initial development environment and test conditions. They are not statistically robust benchmarks or a general ranking of model capabilities.

### Current Prototype Models

The recorded development configuration includes:

- Tool selection: `lfm3:1.7b`
- Local response generation: `qwen3:1.7b`
- Optional remote model setting: `gemini-3.8-flash`

Remote model availability and SDK compatibility must be verified before release.

### Development Status by Feature

| Feature | Status |
|---|---|
| CLI prototype | Existing |
| Ollama integration | Existing |
| Calculator | Existing |
| Restricted workspace file reader | Existing |
| SQLite task history and audit | Existing |
| Security hardening | Under validation |
| Regression test suite | Under validation |
| Hardware detection | Planned |
| Resource-aware model scheduler | Planned |
| Stateful task workflows | Planned |
| Additional productivity tools | Planned |
| Web or desktop interface | Planned |

---

## 4. Design Principles

### 4.1 Local-First, Cloud-Optional

Local execution should be the default. Cloud inference should be optional, visible, configurable, and subject to explicit user approval.

### 4.2 CPU Is a First-Class Target

A dedicated GPU should not be a prerequisite for the core product.

### 4.3 Deterministic Before Generative

Use ordinary application code for arithmetic, file validation, data transformations, policy enforcement, and other deterministic operations.

### 4.4 The Model Proposes; the Application Authorizes

Model output is untrusted input. The application must independently validate tool requests and enforce permissions.

### 4.5 Resource-Aware by Design

Model selection, context size, concurrency, memory usage, and model residency should be based on measured system capabilities.

### 4.6 Graceful Degradation

When local inference is too slow or unavailable, deterministic functionality and simpler workflows should remain useful.

### 4.7 Inspectable and Recoverable

Users should be able to understand what the agent intends to do, which tools it requests, what requires approval, and what happened during execution.

### 4.8 Privacy and User Control

Local information should remain local unless the user explicitly approves external transmission.

### 4.9 Honest Capability Claims

Benchmark conditions, known limitations, failures, and unimplemented features should be documented openly.

---

## 5. Proposed Architecture

The long-term architecture separates the user interface, routing, resource management, model execution, permissions, tools, and persistent state.

### Layer 1 — User Interfaces

Initial interface:

- Command-line interface.

Potential future interfaces:

- Local web interface.
- Native desktop application.

All interfaces should use a shared application core and permission system.

### Layer 2 — Task Intake and Deterministic Router

Responsibilities:

- Normalize and validate incoming requests.
- Identify deterministic tasks.
- Determine whether model inference is needed.
- Estimate task complexity, risk, and resource requirements.
- Request clarification or approval when appropriate.
- Reject actions outside application policy.

The router must not rely solely on an LLM to authorize actions.

### Layer 3 — Resource Manager and Model Scheduler

Responsibilities:

- Detect available system memory and hardware characteristics.
- Discover inference backends and installed models.
- Maintain model metadata and benchmark observations.
- Select models according to task needs and available resources.
- Serialize inference on constrained systems by default.
- Manage model residency and unloading.
- Enforce context, output, time, retry, and resource budgets.
- Support optional cloud inference with explicit consent.

Initial backend direction:

1. Ollama.
2. llama.cpp / llama-server as a later alternative.
3. Optional OpenAI-compatible endpoint adapter after the core stabilizes.

### Layer 4 — Agent Runtime and Workflow State Machine

Longer-term workflows should use explicit states, for example:

`CREATED → PLANNED → AWAITING_APPROVAL → RUNNING → WAITING_FOR_MODEL → COMPLETED / FAILED / CANCELLED`

Planned capabilities:

- Bounded execution steps.
- Bounded retries and timeouts.
- Checkpoints after completed steps.
- Recovery after process restart.
- Cancellation and resume.
- Approval gates before sensitive actions.
- Visible budgets for autonomous operations.

### Layer 5 — Tool Registry and Policy Enforcement

Each tool should declare:

- Tool name and version.
- Input and output schemas.
- Required capabilities.
- Allowed resources.
- Risk level.
- Approval policy.
- Input and output limits.
- Execution timeout.
- Audit metadata.

The application, not the model, makes authorization decisions.

### Initial Tool Roadmap

1. Calculator.
2. Workspace file reader.
3. Workspace file creation and writing with overwrite confirmation.
4. CSV and JSON processing.
5. Document extraction and report creation.
6. Notes and task lists.
7. Browser research with source capture.
8. Communication drafting with approval before sending.
9. User-defined scheduled workflows.

Arbitrary shell execution and arbitrary Python execution should remain disabled by default. Any future execution capability requires a real isolation boundary and a separate security review.

### Layer 6 — Local State and Memory

The intended design separates:

- Current task state.
- Short-term conversation history.
- Tool audit events.
- User-approved durable memory.
- Reusable skills and workflow definitions.

SQLite is the initial persistence direction.

Future work includes schema migrations, configurable retention, export and deletion controls, and encryption-at-rest guidance.

### Layer 7 — Observability and Diagnostics

The agent should measure and expose relevant information locally:

- Model loading time.
- Inference latency.
- Tokens per second where available.
- Process and system memory.
- Selected model and routing reason.
- Context and output sizes.
- Tool calls and approval events.
- Errors, retries, and cancellations.
- Cloud usage and external data transmission.

Secrets and sensitive document contents should not be logged by default.

**Note:** This is the proposed architecture. The current prototype does not implement every layer.

---

## 6. Resource-Efficiency Strategy

The project will explore the following engineering approaches:

1. Avoid unnecessary LLM calls.
2. Use one active model by default on resource-constrained systems.
3. Adapt context size to task requirements and available memory.
4. Divide longer documents into manageable chunks.
5. Recommend quantized models only after measuring performance and resource usage.
6. Bound generation length and execution steps.
7. Limit retries, tool output, file sizes, and network operations.
8. Cache safe and reusable deterministic results.
9. Avoid heavy concurrent inference on low-memory machines.
10. Preserve deterministic functionality when local inference cannot run.

### Proposed Hardware Tiers

These are planning categories, not verified compatibility guarantees.

| Tier | Installed RAM | Intended behavior |
|---|---:|---|
| Minimal | 4 GB or less | Deterministic tools first; local models only after measuring available resources |
| Entry | 8 GB | Small quantized models, CPU-first operation, short context |
| Standard | 16 GB | More model choices and longer workflows after benchmarking |
| Advanced | 32 GB+ | Larger local models and optional concurrency after benchmarking |

Model recommendations must consider available memory, model weights, context/KV cache, runtime overhead, and other running applications.

---

## 7. Security and Privacy

Security is a core engineering requirement.

The project treats user prompts, file contents, retrieved web pages, tool outputs, model responses, and third-party integrations as untrusted input.

### Security Design Goals

- Default-deny tool registry.
- Least-privilege tool capabilities.
- Path canonicalization and symlink-aware workspace checks.
- Explicit approval for sensitive writes and overwrites.
- Explicit approval for external sending and sharing.
- No secrets in model context or logs.
- Strict input and output validation.
- Bounded file, context, output, and execution sizes.
- Audit records for allow, deny, failure, and success outcomes.
- Adversarial prompt-injection testing.
- Dependency and model provenance review.
- User-controlled history retention and deletion.

### Current Limitations

The current prototype uses application-level validation and restrictions. It does not provide a complete operating-system sandbox.

Do not use the experimental prototype to process highly sensitive or confidential information without an independent security review.

A passing test suite is evidence for the cases it exercises, not proof of comprehensive security.

See:

- [Security Policy](SECURITY.md)
- [Security Model](docs/SECURITY_MODEL.md)

---

## 8. Development Roadmap

### v0.3.2 — Security and Reliability Hardening

Focus:

- UTF-8 BOM handling.
- Reusable tool registry and argument validation.
- Calculator AST and resource limits.
- File and output size limits.
- SQLite privacy and retention controls.
- Structured audit outcomes.
- Automated regression tests.
- Preserve the existing CLI and model routing.

**Release gate:** Functional tests pass on the supported environment, security tests cover the current tool surface, and baseline workflows show no regression.

Status: Under validation.

### v0.4 — Resource-Aware Engine

Focus:

- Hardware and available-memory detection.
- Backend and installed-model discovery.
- Model registry and recommendations.
- Context, output, and concurrency budgets.
- Single-model scheduler.
- Configurable keep-alive and unloading behavior.
- Ollama backend first; llama.cpp adapter after benchmarking.

**Release gate:** Publish reproducible CPU-focused results on 8 GB hardware and at least one additional hardware tier.

Status: Planned.

### v0.5 — Stateful Task Planning

Focus:

- Task plan schema.
- Deterministic workflow state machine.
- Step budgets and checkpoints.
- Retry, cancellation, and resume.
- Approval checkpoints.
- Execution trace in the CLI.

**Release gate:** Multi-step workflows recover correctly after errors and process restart.

Status: Planned.

### v0.6 — Productivity Modules

Focus:

- Documents and file workflows.
- CSV and spreadsheet analysis.
- Notes and task lists.
- Research with citations and source capture.
- Communication drafting.
- Scheduled workflows.

**Release gate:** Each module has documented permissions, tests, and measured resource costs.

Status: Planned.

### v0.7 — User Experience

Focus:

- Local web interface or desktop shell.
- Guided setup and hardware profile.
- Model management.
- Benchmark viewer.
- Task history and approvals.
- Diagnostics and skills gallery.

**Release gate:** Beginner installation tests on clean Windows and Linux systems.

Status: Planned.

### v1.0 — Stable Open-Source Release

Focus:

- Stable core interfaces and configuration.
- Database migration and backup strategy.
- Documented security boundaries.
- Reproducible benchmark suite.
- Installation and update process.
- Contribution and release procedures.

Status: Planned.

---

## 9. Research and Related Projects

This project learns from existing work in local AI, lightweight agent runtimes, inference engines, workflow orchestration, and AI security.

Relevant projects and resources include:

| Project / Resource | Area |
|---|---|
| [OpenJarvis](https://github.com/open-jarvis/OpenJarvis) | Hardware-aware local AI |
| [eVi](https://github.com/evi-assistant/evi-ai) | Local AI agent architecture |
| [Nanobot](https://github.com/HKUDS/nanobot) | Lightweight Python agent |
| [smolagents](https://github.com/huggingface/smolagents) | Minimal agent framework |
| [LangGraph](https://docs.langchain.com/oss/python/langgraph/) | Stateful workflows |
| [Ollama](https://github.com/ollama/ollama) | Local model runtime |
| [llama.cpp](https://github.com/ggml-org/llama.cpp) | CPU inference and quantization |
| [OWASP GenAI Security Project](https://genai.owasp.org/) | AI application security |

The project does not claim to be the first local agent, CPU-based agent, or hardware-aware AI assistant.

Our differentiation must be demonstrated through measured low-resource performance, reproducible benchmarks, simple installation, and practical workflows.

---

## 10. Benchmarking Standards

Performance claims should be reproducible and accompanied by relevant test conditions.

Benchmark reports should include:

- Hardware and operating system.
- Model name, tag, revision, and quantization where known.
- Inference backend and runtime version.
- Context and generation settings.
- Warm or cold model state.
- Latency and throughput.
- Peak RAM and VRAM where measured.
- Task success criteria.
- Number of test runs.
- Failures and limitations.

Exploratory one-run comparisons must be labelled as preliminary.

---

## 11. What to Do Next

Low-Resource Agent is being developed incrementally. The immediate priorities are to validate the current prototype, establish reproducible tests, and build a reliable foundation for the resource-aware engine.

### Current Priorities

1. Complete v0.3.2 validation on the development machine.
2. Publish the prototype source and regression tests after review.
3. Establish automated Python testing through GitHub Actions.
4. Improve documentation and installation instructions.
5. Begin hardware profiling and resource-aware model selection.
6. Invite testing on other low-memory and CPU-only systems.

### How You Can Help

You do not need to be an experienced AI engineer to contribute.

- **Developers:** Improve the Python runtime, model integrations, tools, and resource management.
- **Testers:** Test on older computers, CPU-only systems, and low-memory environments.
- **Researchers:** Compare local models, inference backends, and quantization strategies.
- **Documentation contributors:** Improve guides, tutorials, examples, and troubleshooting.
- **Users:** Report bugs, suggest useful workflows, and share reproducible feedback.

We particularly welcome feedback from people using older laptops, computers with limited RAM, and systems without dedicated GPUs.

---

## 12. Contributing to the Project

Contributions are welcome!

Whether you are fixing a bug, proposing a feature, improving documentation, or testing on different hardware, your contribution can help make local AI more accessible.

Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting a contribution.

### Step 1 — Explore the Project

Start by reading:

- [Project Plan](docs/PROJECT_PLAN.md)
- [Architecture Overview](docs/ARCHITECTURE.md)
- [Security Model](docs/SECURITY_MODEL.md)
- [Contributing Guidelines](CONTRIBUTING.md)

Review existing GitHub Issues before proposing a new feature or reporting a duplicate bug.

### Step 2 — Fork the Repository

Open the repository:

https://github.com/ashiquebiniqbal/ashiquebiniqbal-low-resource-agent

Click **Fork** to create your own copy under your GitHub account.

### Step 3 — Clone Your Fork

Replace `YOUR-USERNAME` with your GitHub username:

```bash
git clone https://github.com/YOUR-USERNAME/ashiquebiniqbal-low-resource-agent.git
cd ashiquebiniqbal-low-resource-agent
```

Add the original repository as an upstream remote:

```bash
git remote add upstream https://github.com/ashiquebiniqbal/ashiquebiniqbal-low-resource-agent.git
```

### Step 4 — Create a Feature Branch

Create a separate branch for your contribution:

```bash
git switch -c feature/your-feature-name
```

For a bug fix:

```bash
git switch -c fix/issue-description
```

Avoid making changes directly on your `main` branch.

### Step 5 — Make and Test Your Changes

Make your changes and add relevant tests.

When the test suite is available, run:

```bash
python -m unittest discover -s tests -p "test_*.py" -v
```

Check your changes:

```bash
git diff --check
git status
```

Do not include API keys, passwords, private documents, local databases, or personal environment files in a contribution.

### Step 6 — Commit and Push

Stage your changes:

```bash
git add .
```

Commit them:

```bash
git commit -m "Describe your contribution"
```

Push your feature branch:

```bash
git push -u origin feature/your-feature-name
```

### Step 7 — Open a Pull Request

Visit your fork on GitHub.

Select **Compare & pull request**, and submit your pull request against the original repository's `main` branch.

Please explain:

- What you changed.
- Why the change is useful.
- How you tested it.
- Which operating systems and hardware configurations you used.
- Any known limitations or compatibility concerns.

We review contributions with an emphasis on simplicity, resource efficiency, maintainability, and security.

### Keeping Your Fork Updated

Before beginning new work, synchronize your local main branch with the original repository:

```bash
git fetch upstream
git switch main
git pull --ff-only upstream main
```

Then create a new feature branch.

---

## 13. Community and Collaboration

Low-Resource Agent is an open-source project built around a simple idea:

# Useful AI should not require expensive hardware.

We welcome developers, researchers, students, hardware enthusiasts, and anyone interested in making local AI more accessible.

You can help by:

- Sharing reproducible benchmarks from your hardware.
- Testing different Ollama models and inference backends.
- Reporting bugs and suggesting improvements.
- Contributing code, documentation, or test cases.
- Discussing ideas for more efficient and practical agent workflows.
- Testing installation and usability on different operating systems.

### Where to Participate

- **Bug reports:** Use [GitHub Issues](https://github.com/ashiquebiniqbal/ashiquebiniqbal-low-resource-agent/issues).
- **Feature ideas:** Open an issue describing the use case and expected behavior.
- **Code contributions:** Submit a Pull Request.
- **Security concerns:** Follow the private reporting guidance in [SECURITY.md](SECURITY.md).

Every useful contribution matters.

Let's make local-first AI more accessible, practical, and resource-efficient — together.

**Thank you for exploring and contributing to Low-Resource Agent!**

---

## License

This project is distributed under the **Apache License 2.0**.

You are free to use, modify, and distribute the software according to the terms of the license.

See the [LICENSE](LICENSE) file for the complete license text.
