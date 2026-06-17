---
name: implement
description: Implement a code change, typically a PR, as specified by the user. Use when the users wants to implement a code change, where the goals and initial proposed design are already available.
---

If starting from scratch, use a git worktree to implement the requested code change (e.g. a specific Linear/Github Issue), if the requested change is unclear, ask.

Create a short branch name. Commit in logical chunks, with concise commit messages/details. If relevant, don't generate migrations until the final commit to avoid multiple migrations for a single PR.

Don't push commits unless the user explicitly tell you to.

Makes sure you explore the existing code structure, e.g. libraries, modules, etc.

Use the principles from the /improve-codebase-architecture skill to design the implementation.
