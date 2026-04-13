---
name: self-documenting-code
description: "Improve readability by fixing names and eliminating unnecessary comments. Replace magic numbers, abbreviations, and comments with expressive names, variables, functions, and types."
user-invocable: true
allowed-tools: Read, Edit, Grep, Glob, Bash
---

# Self-Documenting Code

When invoked, review the specified code for naming problems, unnecessary comments, and missing documentation. Refactor names and code structure so that inline comments become redundant and the code speaks for itself. Add concise doc comments to functions and classes that lack them. The goal: remove the clutter, keep and add the documentation.

If the user has not specified a scope (file or directory), ask them which file or directory to focus on before proceeding.

## Core Rules: Naming

1. **Never use single-letter variable names.** The only exception is `i`, `j`, `k`, etc. as loop indices in trivial loops of a few lines. Everywhere else, use a descriptive name.

2. **Limit abbreviation.** Write `documentManager` not `dm`. Prefer `transaction` over `txn`. Autocompletion eliminates the typing cost; abbreviations force every reader to decode them. The only exception is common or idiomatic abbreviations, such as ctx in Go.

3. **Do not encode types in variable names** (no Hungarian notation). `count` not `intCount`. `users` not `arrUsers`. Let the type system convey type information.

4. **Include units in names when the type does not convey them.** `delaySeconds` not `delay`. `distanceMeters` not `distance`. `maxRetries` not `max`. Even better: use a typed value (`Duration`, `TimeSpan`, `chrono::duration`) if the language supports it, eliminating unit ambiguity entirely.

5. **Do not prefix interfaces with `I`.** Write `Serializable` not `ISerializable`. The consumer does not care whether it is an interface, abstract class, or concrete class. **Exception:** In C# codebases where the `I` prefix convention is already established project-wide, follow the existing convention for consistency.

6. **Do not use `Base`, `Abstract` or similar in class names.** If you struggle to name the parent, rename the child to be more specific and give the parent the natural general name. `Email` (parent) and `NotificationEmail` (child), not `BaseEmail` and `Email`.

7. **Never name a class or package `Utils`, `Helper`, `Manager`, or `Common`.** These are code-smell names that indicate structural problems. Move methods/functions to the types/packages they operate on, create descriptive classes, or make collections first-class types. Standard libraries never have a "Utils" module because everything is sorted into properly named modules.

## Core Rules: Eliminating Comments

8. **Replace magic numbers with named constants.** `if (status == ORDER_SHIPPED)` not `if (status == 3)`. `timeoutMs = SECONDS_PER_DAY * 1000` not `timeoutMs = 86400000`.

9. **Replace complex boolean conditions with named variables or predicate functions.** If a condition needs a comment to explain it, break it into named booleans or extract it into a function whose name IS the explanation.

10. **Use types to convey semantics instead of comments.** Return `Optional<User>` instead of returning `null` with a comment "returns null if not found". Use `unique_ptr` instead of a raw pointer with a comment about ownership. Use an enum instead of magic integers with comments mapping values.

11. **Delete comments that merely restate what the code does.** `i += 1  // increment i` adds nothing. If the code is clear, the comment is noise.

12. **Delete stale comments that contradict the code.** A comment that says "returns a list" when the code returns a map is worse than no comment, it actively misleads.

13. **Preserve justified comments:**
    - Comments explaining non-obvious performance optimizations ("we use a bitmap here instead of a hash set because the key space is bounded to 256 values")
    - Links to algorithm sources, specifications, or regulatory requirements
    - Comments explaining *why* code looks unusual, not *what* it does

## Core Rules: Documentation

Removing unnecessary comments does NOT mean removing documentation. Inline clutter is the enemy; doc comments are the ally. The distinction:

- **Inline comments** (inside function bodies) explain *how* code works → usually a sign the code should be clearer. Remove these by making the code self-explanatory.
- **Doc comments** (above functions, classes, methods) explain *what* something does for its callers → these are developer documentation. Keep and add these.

14. **Every function and method should have a doc comment.** The doc comment should be concise, typically one sentence. It describes *what* the function does and *what* it returns, not *how* it works internally. Describe the interface, not the implementation.

15. **Every class should have a doc comment.** One or two sentences describing what the class represents and its responsibility.

16. **Use the language's standard doc format.** JSDoc (`/** */`) in JavaScript/TypeScript, docstrings (`"""..."""`) in Python, JavaDoc (`/** */`) in Java, `///` in Rust and C#, etc.

17. **Doc comments should not repeat the function signature.** Do not write `@param name the name` -- the type and parameter name already say that. Only document parameters when their purpose or constraints are not obvious from the name and type alone.

18. **When types already express everything, keep the doc comment minimal.** A function `findByEmail(email: string): Optional<User>` needs only a brief doc comment like `/** Finds a user by their email address. */` -- the return type already communicates the "not found" case.

## Procedure

1. **Scan for magic numbers.** Find numeric literals used in conditions, assignments, and function calls. Replace each with a named constant whose name explains the value's meaning.

2. **Scan for poor names.** Find abbreviated, single-letter, or type-prefixed variable/function names. Rename them to be descriptive. Follow the conventions of the codebase's language.

3. **Scan for comments.** For each comment:
   - If it explains *what* the code does → refactor the code to be self-explanatory (rename variables, extract functions, introduce named constants) and delete the comment.
   - If it explains *why* the code does something non-obvious → keep it.
   - If it is stale or contradicts the code → delete it.
   - If it is public API documentation → keep it, but check if it can be made more concise by improving the API's naming and types.

4. **Scan for missing doc comments.** Find functions, methods, and classes that lack a doc comment. Add a concise doc comment to each, describing what it does for its callers. Do not describe internals.

5. **Scan for `Utils`/`Helper`/`Manager`/`Common` classes or packages.** For each, propose a restructuring: move methods to the types they operate on, or create descriptive classes/packages.

6. **Scan for `Base`/`Abstract` prefixed classes and `I`-prefixed interfaces** (in non-C# code). Propose renaming.

7. **Present changes** to the user with brief explanations.

## Adapt to the User's Language

Apply naming conventions appropriate to the language: `snake_case` in Python/Rust, `camelCase` in JavaScript/Java, `PascalCase` for types in C#/Go, etc. Use language-specific type features (Optional, Result, enums, newtypes) where available.

---

## Examples

### Example A: Magic Numbers and Naming

A function with cryptic variable names and unexplained numeric literals:

**Before:**

```python
def proc(o):
    if o.s == 3:
        t = o.d * 86400
        if t > 604800:
            o.s = 4
            notify(o.u, 1)
        else:
            sched(o.id, t)
    elif o.s == 7:
        cleanup(o.id)
```

A reader must guess what `s`, `d`, `t`, `3`, `86400`, `604800`, `4`, `7`, and `1` mean. The function name `proc` tells you nothing.

**After:**

```python
SECONDS_PER_DAY = 86400
MAX_SCHEDULE_WINDOW_SECONDS = 7 * SECONDS_PER_DAY

ORDER_SHIPPED = 3
ORDER_EXPIRED = 4
ORDER_CANCELLED = 7

NOTIFICATION_DELIVERY_REMINDER = 1


def schedule_or_expire_order(order):
    if order.status == ORDER_SHIPPED:
        delivery_window_seconds = order.estimated_days * SECONDS_PER_DAY
        if delivery_window_seconds > MAX_SCHEDULE_WINDOW_SECONDS:
            order.status = ORDER_EXPIRED
            notify(order.user_id, NOTIFICATION_DELIVERY_REMINDER)
        else:
            schedule_followup(order.id, delivery_window_seconds)

    elif order.status == ORDER_CANCELLED:
        cleanup_order(order.id)
```

Every value has a name that explains its purpose. The function name describes what it does. A reader does not need any external context to understand this code.

---

### Example B: Comments Replaced by Named Variables

A complex condition with a long comment explaining the logic:

**Before:**

```javascript
// Apply a discount if the customer has been a member for more than 2 years
// and has made at least 5 purchases this quarter, OR if they have a corporate
// account with an active discount agreement that hasn't expired
if (
    (customer.memberSince < Date.now() - 63072000000 &&
     customer.purchasesThisQuarter >= 5) ||
    (customer.accountType === "corporate" &&
     customer.discountAgreement &&
     customer.discountAgreement.expiresAt > Date.now())
) {
    applyDiscount(order, customer.discountRate);
}
```

The comment is needed because the condition is unreadable. But the comment can become stale when someone changes the condition without updating it.

**After:**

```javascript
const TWO_YEARS_MS = 2 * 365 * 24 * 60 * 60 * 1000;
const LOYALTY_PURCHASE_THRESHOLD = 5;

const isLongTermMember = customer.memberSince < Date.now() - TWO_YEARS_MS;
const isFrequentBuyer = customer.purchasesThisQuarter >= LOYALTY_PURCHASE_THRESHOLD;
const hasLoyaltyDiscount = isLongTermMember && isFrequentBuyer;

const hasCorporateDiscount =
    customer.accountType === "corporate" &&
    customer.discountAgreement?.expiresAt > Date.now();

if (hasLoyaltyDiscount || hasCorporateDiscount) {
    applyDiscount(order, customer.discountRate);
}
```

The `if` statement now reads like the original comment: "if has loyalty discount or has corporate discount." The comment is deleted because the code says exactly the same thing. If someone changes the loyalty threshold from 5 to 10, the constant `LOYALTY_PURCHASE_THRESHOLD` forces them to update in one place; no stale comment.

---

### Example C: Utils Class Decomposition

A grab-bag utility class that has accumulated unrelated functions:

**Before:**

```typescript
class StringUtils {
    static formatCurrency(amount: number, currencyCode: string): string { /* ... */ }
    static truncateWithEllipsis(text: string, maxLength: number): string { /* ... */ }
    static parsePhoneNumber(raw: string): PhoneNumber { /* ... */ }
    static slugify(text: string): string { /* ... */ }
    static maskCreditCard(cardNumber: string): string { /* ... */ }
    static extractDomain(email: string): string { /* ... */ }
}

// Usage scattered across the codebase:
const price = StringUtils.formatCurrency(order.total, "USD");
const preview = StringUtils.truncateWithEllipsis(post.body, 140);
const phone = StringUtils.parsePhoneNumber(formInput.phone);
const slug = StringUtils.slugify(article.title);
const masked = StringUtils.maskCreditCard(payment.cardNumber);
const domain = StringUtils.extractDomain(user.email);
```

"StringUtils" tells you nothing about what these functions do or why they are grouped together. They handle money, text display, phone numbers, URLs, and credit cards -- completely unrelated concerns.

**After:**

```typescript
class Money {
    constructor(readonly amount: number, readonly currencyCode: string) {}

    format(): string { /* ... */ }
}

class PhoneNumber {
    static parse(raw: string): PhoneNumber { /* ... */ }

    format(): string { /* ... */ }
}

class TextFormatter {
    static truncateWithEllipsis(text: string, maxLength: number): string { /* ... */ }
    static slugify(text: string): string { /* ... */ }
}

class CreditCard {
    constructor(readonly number: string) {}

    masked(): string { /* ... */ }
}

class EmailAddress {
    constructor(readonly address: string) {}

    get domain(): string { /* ... */ }
}

// Usage is now natural and discoverable:
const price = new Money(order.total, "USD").format();
const preview = TextFormatter.truncateWithEllipsis(post.body, 140);
const phone = PhoneNumber.parse(formInput.phone);
const slug = TextFormatter.slugify(article.title);
const masked = new CreditCard(payment.cardNumber).masked();
const domain = new EmailAddress(user.email).domain;
```

Each class represents a real concept. Methods live on the types they operate on. `TextFormatter` groups the two genuinely related text-display operations. There is no "Utils" catch-all.

---

### Example D: Types Replacing Comments

Functions that use comments to document what their types cannot express:

**Before:**

```java
public class UserRepository {
    /**
     * Finds a user by email.
     * @return the User, or null if no user exists with that email
     */
    public User findByEmail(String email) {
        // ...
    }

    /**
     * Calculates estimated delivery time.
     * @param weight package weight in grams
     * @param distance distance to destination in kilometers
     * @return estimated delivery time in hours, or -1 if route is unavailable
     */
    public int estimateDeliveryTime(int weight, int distance) {
        // ...
    }
}
```

The comments are load-bearing. Without them, a caller does not know that `findByEmail` might return `null` or that `-1` means "unavailable." But the comments can become stale if someone changes the behavior.

**After:**

```java
public class UserRepository {
    public Optional<User> findByEmail(String email) {
        // ...
    }

    public Optional<Duration> estimateDeliveryTime(Weight weight, Distance distance) {
        // ...
    }
}
```

`Optional<User>` makes it impossible to forget the null case -- the compiler forces the caller to handle it. `Optional<Duration>` replaces the magic `-1` sentinel value. `Weight` and `Distance` are typed values that carry their units, eliminating the "in grams" and "in kilometers" comments. The type signatures now convey everything the comments used to say, and the compiler enforces correctness.

---

### Example E: Remove Inline Clutter, Add Doc Comments

A file with many inline comments explaining obvious code, but no doc comments on any of the functions:

**Before:**

```typescript
function calculateShippingCost(items, destination) {
    // Calculate the total weight
    let totalWeight = 0;
    for (const item of items) {
        totalWeight += item.weight; // add weight
    }

    // Get the rate for this destination
    const rate = getRate(destination);

    // Apply the rate to get the cost
    const cost = totalWeight * rate;

    // Free shipping for orders over 50
    if (cost > 50) {
        return 0;
    }

    // Return the cost
    return cost;
}

function getRate(destination) {
    // Check if it's domestic
    if (destination.country === "US") {
        return 0.5; // domestic rate
    }
    // International rate
    return 2.0;
}

function formatForDisplay(cost) {
    // Format as dollars
    return "$" + cost.toFixed(2);
}
```

Every inline comment either restates the code or explains a magic number. None of the functions have doc comments, so a caller must read the entire body to understand what each function does.

**After:**

```typescript
const FREE_SHIPPING_THRESHOLD = 50;
const DOMESTIC_RATE_PER_KG = 0.5;
const INTERNATIONAL_RATE_PER_KG = 2.0;

/** Calculates shipping cost for a set of items to a destination. Returns 0 if the order qualifies for free shipping. */
function calculateShippingCost(items: CartItem[], destination: Address): number {
    const totalWeight = items.reduce((sum, item) => sum + item.weight, 0);
    const cost = totalWeight * getShippingRate(destination);

    return cost > FREE_SHIPPING_THRESHOLD ? 0 : cost;
}

/** Returns the per-kilogram shipping rate for a destination based on whether it is domestic or international. */
function getShippingRate(destination: Address): number {
    if (destination.country === "US") {
        return DOMESTIC_RATE_PER_KG;
    }
    return INTERNATIONAL_RATE_PER_KG;
}

/** Formats a cost as a USD dollar string (e.g. "$12.50"). */
function formatForDisplay(cost: number): string {
    return "$" + cost.toFixed(2);
}
```

Every inline comment is gone -- the code is clear without them. Named constants replace magic numbers. But every function now has a concise doc comment that tells callers what it does and what it returns, without describing the implementation. A developer using these functions can read the doc comment and signature without ever looking at the body.
