# Spearmint

Structural refactoring skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code/skills). Five commands that flatten nesting, fix names, simplify abstractions, inject dependencies, and convert loops to functional pipelines.

## Why?

AI coding agents are fast, but they often write code with comments that repeat what the code already says, nest logic 6+ levels deep instead of using guard clauses, reach for abstract class hierarchies when a plain function would do, and scatter magic numbers through business logic. The code works, but it's harder to review, maintain, and change.

Spearmint is designed to run after an agent's initial pass. Each skill targets a specific class of structural problem with concrete refactoring techniques, so the output is code that's easier work with before it's merged.

## Skills

| Skill | What it does |
|-------|-------------|
| `/flatten-code` | Reduces nesting beyond 3 levels using guard clauses, early returns, and extraction into named functions |
| `/self-documenting-code` | Replaces magic numbers, abbreviations, and comment-dependent code with expressive names, constants, and types |
| `/simplify-abstractions` | Removes _premature_ interfaces and inheritance, replaces with composition, ensures abstractions earn their cost |
| `/dependency-injection` | Extracts interfaces for dependencies, moves construction to factories, enables testing with fakes |
| `/functional-patterns` | Converts imperative loops to `filter`/`map`/`reduce` pipelines, adapted to language idioms (JS chaining, Python comprehensions, Java Streams, etc.) |

Each skill is language-agnostic and includes detailed before/after examples. See the [skill definitions](skills/) for full rules and procedures.

## Installation

**Per-project** (recommended):

```sh
cp -r skills/ your-project/.claude/skills/
```

**Global** (all projects):

```sh
cp -r skills/* ~/.claude/skills/
```

## Usage

Invoke any skill by name inside Claude Code:

```
/flatten-code
/self-documenting-code
/simplify-abstractions
/dependency-injection
/functional-patterns
```

Each skill scans the files you point it at, identifies problems, and applies targeted refactorings.

## License

MIT. See [LICENSE](LICENSE).
