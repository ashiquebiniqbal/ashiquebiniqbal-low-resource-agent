# Security Model

**Status:** Design requirements for an experimental prototype, not a completed security audit.

## Trust boundaries

Treat user prompts, workspace files and document contents, retrieved web pages, tool outputs, model responses and proposed arguments, third-party skills and integrations, and downloaded model/dependency artifacts as untrusted until validated.

## Core rules

1. Default deny for tools not explicitly registered.
2. Application code—not the model—authorizes each action.
3. Grant each tool only the capabilities it needs.
4. Canonicalize and validate paths at the point of use.
5. Reject hidden configuration and secret files from general workspace reading.
6. Bound file sizes, expression complexity, input/output lengths, time, retries, and workflow steps.
7. Require approval before sensitive writes, overwrites, external sends, or other high-impact actions.
8. Keep secrets out of prompts, history, logs, and test fixtures.
9. Audit allow, deny, failure, and success outcomes while minimizing stored content.
10. Provide user-controlled history retention and deletion.

## Prototype limitations

The existing prototype uses application-level restrictions. These do not equal an OS-level sandbox. They cannot by themselves isolate a compromised Python process or protect against every filesystem race, dependency issue, or malicious local process.

Do not process highly sensitive data with the prototype without an independent review. Do not introduce arbitrary shell/Python execution without a separately designed isolation boundary.

## Security test plan

- path traversal, absolute paths, UNC paths, symlinks and junctions;
- hidden files, environment files, and unsupported extensions;
- Unicode normalization and filename ambiguity;
- prompt injection in text/Markdown/CSV/JSON;
- malformed files and oversized input;
- malicious tool outputs and model-generated arguments;
- unknown tools and extra arguments;
- calculator AST depth, node count, exponents, and magnitude;
- secret exposure through prompts, terminal output, audit, and SQLite;
- approval bypass, cancellation, and retry behavior;
- resource exhaustion, timeouts, and queue overload.

Tests should use synthetic data and exercise documented behavior. Passing tests are not proof of complete security.
