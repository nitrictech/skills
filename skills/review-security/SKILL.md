---
name: review-security
description: "Audit an entire source tree for security vulnerabilities, validating findings against framework and library documentation to avoid false positives. Use when the user wants a project-wide security review or asks to find security issues across the codebase."
user-invocable: true
allowed-tools: Read, Grep, Glob, Bash, WebFetch, Task
---

# Review Security

Exhaustively audit the source tree starting from the path the user specified, or the project root if none. Use sub-agents to review logic paths and code modules in parallel.

Before flagging an issue, check whether a framework, library or project module in use already mitigates it. Read the relevant docs. Report only what the surrounding code, dependencies, and documentation do not already handle.

Group findings by severity and present them to the user. Document them to a file if asked.
