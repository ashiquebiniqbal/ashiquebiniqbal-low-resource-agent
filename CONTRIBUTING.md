# Contributing

Thank you for considering a contribution to Low-Resource Agent.

## Before opening an issue

- Check existing issues and discussions for duplicates.
- Include your operating system, Python version, Ollama version, model tag, and relevant configuration.
- Remove API keys, tokens, personal data, and private file contents from logs.
- For performance reports, include the hardware and exact test conditions.

## Development principles

- Keep the core small and understandable.
- Prefer deterministic code for arithmetic, validation, file handling, policy enforcement, and state changes.
- Treat model output and external content as untrusted input.
- Do not allow a model to authorize its own tool calls.
- Keep tools narrowly scoped and validate arguments in application code.
- Require explicit approval for sensitive writes, external transmission, and other high-impact operations.
- Avoid mandatory Docker, GPU, or heavyweight orchestration dependencies unless a concrete requirement justifies them.
- Document resource costs and failure modes rather than presenting unmeasured performance claims.

## Pull requests

A good pull request should:

1. Explain the problem and expected behavior.
2. Keep changes focused and reasonably small.
3. Include tests for new behavior and security-relevant edge cases.
4. Update documentation and configuration examples.
5. Confirm that no secrets, local databases, workspace contents, or generated model files are included.
6. State which checks were run and on what environment.

Do not describe a test as passing unless it was actually executed. Clearly identify checks that were not run.

## Security-sensitive changes

Do not add arbitrary shell execution, arbitrary Python execution, or unrestricted filesystem access as a convenience feature. Such capabilities require a separate threat model and an actual isolation boundary, not only a prompt instruction or subprocess wrapper.

For suspected vulnerabilities, follow [SECURITY.md](SECURITY.md) rather than publishing exploit details in a public issue.
