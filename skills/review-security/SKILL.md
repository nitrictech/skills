---
name: review-security
description: Delve deep into the project in search of security issues, review all paths methodically, find information on used frameworks and libraries to confirm your finding are valid. Use when the users wants to carefully search for or fix security vulnerabilities or mentions "review security".
---

Perform a detailed security review the entire source tree methodically, starting from a location provided by the user, if no location is specified start at the root. Enlist sub-agents to delve into all paths in search of issues.

If an issue is suspected, ensure it is not already mitigated by libraries or other modules, search for documentation when required.

Group issues by severity and present them to the user or document them if requested.
