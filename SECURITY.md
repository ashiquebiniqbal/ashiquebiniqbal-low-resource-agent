# Security Policy

## Project status

Low-Resource Agent is experimental software under active development. The current prototype is not production-ready and has not undergone a comprehensive independent security audit.

## Reporting a vulnerability

Please do not publish API keys, personal information, private documents, or a working exploit against a live system in a public issue.

Use GitHub's private vulnerability reporting feature for this repository if it is enabled. Otherwise, contact the repository owner privately through a verified contact method listed on the owner's GitHub profile.

Include:

- affected version or commit;
- operating system and relevant environment details;
- a concise description of the security impact;
- steps to reproduce using synthetic, non-sensitive data;
- any suggested mitigation.

## Current security limitations

The prototype's application-level path checks, input validation, tool registry, and user approvals are not an operating-system sandbox. They do not establish isolation against a compromised process, malicious local user, filesystem race, or vulnerability in a dependency/runtime.

A passing test suite covers only its tested cases. It does not prove complete security.

Until a reviewed isolation model exists:

- do not expose arbitrary shell or Python execution;
- do not process highly sensitive or confidential documents;
- do not put secrets into prompts, logs, issue reports, or test fixtures;
- review every tool permission and external network action;
- keep cloud inference opt-in and require user approval before sending content externally.

## Supported releases

Security fixes and advisories will be documented here as the project establishes versioned releases.
