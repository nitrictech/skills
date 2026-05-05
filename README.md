# Skills

Five skills we use to clean up agent-written code before reviewing it.

## The problem

Agents produce code that works but isn't pleasant to live with. Inline comments restating the line below. Six-deep `if` pyramids with the actual work buried at the bottom. Magic numbers nobody named. Single-implementation interfaces and abstract base classes nobody extends.

This looks like it comes from training data. Public code (tutorials, examples, etc.) leans on comments and structural padding to teach those who don't yet know how to program. Agents have absorbed the teaching style as the default, so it sticks around in the output.

Real code is written for engineers who already know the language. The things worth documenting are facts specific to the project.

We've been writing code this way for years (mostly). These skills are the same standards, codified so an agent can apply them on demand.

## Quickstart

```bash
npx skills@latest add nitrictech/skills
```

Pick the skills you want and which agents to install them on.

## Usage

Skills are independent, run whichever targets issues you see in the diff. Apply each to a directory or file:

```
/flatten src/checkout/
/self-document src/checkout/order.ts
```

## The skills

### `/flatten` — when code drifts to the right

```typescript
function process(orders: Order[]) {
    if (orders) {
        for (const order of orders) {
            if (order.valid) {
                if (order.total > 0) {
                    // ... the actual work, six levels deep
                }
            }
        }
    }
}
```

`/flatten` extracts logic into named functions and inverts conditions into guard clauses, so happy paths read top-to-bottom and failure cases get handled at the door. → [SKILL.md](./skills/flatten/SKILL.md)

### `/self-document` — when names lie and comments repeat

```typescript
// increment the count
n += 1;

if (status === 3) { /* ... */ }

class StringUtils { /* ... */ }
```

`/self-document` replaces magic numbers with named constants, rewrites cryptic variables, deletes comments that restate the code, and adds doc comments where callers actually need them. Where types can carry the meaning, types do (`Optional<User>` over `null` plus a comment explaining the null case). → [SKILL.md](./skills/self-document/SKILL.md)

### `/simplify-abstractions` — when interfaces have one implementation

An `IPaymentProcessor` interface, a `PaymentProcessorBase` abstract class, and exactly one concrete `StripePaymentProcessor`. Or a four-level inheritance tree to share two methods.

`/simplify-abstractions` removes premature interfaces, replaces inheritance with composition where the relationship is structural rather than hierarchical, and demands abstractions earn their cost. → [SKILL.md](./skills/simplify-abstractions/SKILL.md)

### `/dependency-injection` — when classes wire themselves to the world

A class news up its database client, its HTTP client, and its clock right in the constructor. Now nothing here can be tested without a real database, and changing the HTTP library means editing every consumer.

`/dependency-injection` extracts interfaces for these dependencies, moves construction to factories, and rewires consumers to accept their dependencies as parameters — making fakes possible. → [SKILL.md](./skills/dependency-injection/SKILL.md)

### `/functional-patterns` — when for-loops do filter-map-reduce work

```typescript
const shipped = [];
for (let i = 0; i < orders.length; i++) {
    if (orders[i].status === "shipped") {
        shipped.push({ id: orders[i].id, total: orders[i].total });
    }
}
```

`/functional-patterns` rewrites these into `filter`/`map`/`reduce` chains in JavaScript and TypeScript, list comprehensions in Python, and Streams in Java — declarative pipelines that say what they do, not how. → [SKILL.md](./skills/functional-patterns/SKILL.md)

## Acknowledgements

[Code Aesthetic](https://www.youtube.com/@CodeAesthetic) covers similar patterns on YouTube with crisp examples — particularly *Naming Things in Code*, *Don't Write Comments*, and *Why You Shouldn't Nest Your Code*. We used his transcripts as a sanity check when codifying our own rules. Worth watching for clear takes on the same problems.

## License

MIT. See [LICENSE](LICENSE).
