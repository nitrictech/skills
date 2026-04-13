# Spearmint

A collection of [Claude Code skills](https://docs.anthropic.com/en/docs/claude-code/skills) that review and refactor code for structural quality. They help reduce nesting, improve names, simplify abstractions, apply dependency injection, and convert imperative loops to functional patterns.

## Skills

### `/flatten-code`

Finds deeply nested code, typically identified by more than 3 levels, and refactors them using **inversion** (guard clauses, early returns) and **extraction** (pulling inner blocks into named functions). The result is flat code where the top-level function reads like a table of contents.

### `/self-documenting-code`

Fixes naming problems and eliminates unnecessary comments. Replaces magic numbers with named constants, abbreviations with full names, complex conditions with named booleans, and comment-dependent APIs with expressive types. Decomposes `Utils`/`Helper` classes into properly named types.

### `/simplify-abstractions`

Evaluates abstractions and inheritance hierarchies for over-coupling. Removes premature interfaces (1-2 implementations with no deferred usage), replaces inheritance with composition, and ensures abstractions earn their coupling cost. Explains when to reintroduce an abstraction later.

### `/dependency-injection`

Refactors tightly coupled code to accept dependencies as parameters. Extracts interfaces for external services, moves construction logic to factories, and centralizes wiring in a composition root. Demonstrates how the refactored structure enables testing with fakes and mocks.

### `/functional-patterns`

Converts imperative loops into declarative data pipelines using `filter`, `map`, `reduce`, `sort`, and `slice`. Adapts to language idioms (JS method chaining, Python comprehensions, Java Streams, C# LINQ, Rust iterators). Leaves side-effectful loops alone.

## Installation

### Per-project (recommended)

Copy the `skills/` directory into the `.claude/skills/` dir of your project:

```sh
cp -r skills/ /path/to/your-project/.claude/skills/
```

Skills will be available when running Claude Code inside that project.

### Global (all projects)

Copy the skill directories into your personal Claude Code config:

```sh
cp -r skills/* ~/.claude/skills/
```

Skills will be available in every project.

## Usage

Invoke any skill by name inside Claude Code:

```
/flatten-code
/self-documenting-code
/simplify-abstractions
/dependency-injection
/functional-patterns
```
