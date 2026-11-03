# Type systems

> A type system is a set of rules for classifying values and checking that operations receive compatible inputs; it is a tool for making invalid programs harder to write.

**Level:** beginner  
**Time:** 9 minutes  
**Scope:** static vs dynamic, strong vs weak, structural vs nominal, inference, and soundness. It does not teach type theory.

## The mental model

A type is a contract. `string` says “this value can be treated as text”; `User` says “this object has the fields the rest of the program expects.” The type checker tries to prove that every operation respects the contract.

Types are not just labels for the compiler. They are documentation, editor completions, refactoring support, and a way to make illegal states unrepresentable.

Where the analogy breaks: a contract can be checked before the work starts (static) or at runtime (dynamic), and some languages deliberately allow escape hatches.

## The two axes

People often say “static vs dynamic” when they mean one of four different things.

| Axis | Question | Examples |
|---|---|---|
| **Static vs dynamic** | When are types checked? | TypeScript/Java (static); Python/Ruby (dynamic) |
| **Strong vs weak** | How easily do values convert implicitly? | Python (strong); JavaScript (weaker coercion) |
| **Nominal vs structural** | Is compatibility by name or by shape | Java (nominal); TypeScript (structural) |
| **Sound vs unsound** | Are all runtime values covered by the type system? | Rust (mostly sound); TypeScript (deliberately unsound) |

“Strong” and “weak” are informal. A more useful question: *what conversions happen without an explicit cast?*

## Static typing

The checker runs before execution and rejects programs it cannot prove safe.

```ts
type User = { id: string; email: string };

function sendEmail(user: User) {
  return mailer.send(user.email);
}

sendEmail({ id: "u1", email: "a@example.com" }); // ok
sendEmail({ id: "u1" });                         // compile error
```

Benefits:

- Errors surface earlier.
- Editors can autocomplete and refactor safely.
- Types serve as machine-checked documentation.
- Some optimisations become possible.

Costs:

- More upfront annotation (reduced by inference).
- Complex types can be harder to read than the code.
- A type error does not prove business logic is correct.
- False confidence at boundaries where untyped data enters.

## Dynamic typing

Types are checked at runtime, often via the operations themselves.

```python
def send_email(user):
    return mailer.send(user["email"])
```

This is flexible and concise. It also means a typo may not surface until the code path runs. Modern dynamic languages mitigate this with optional annotations, runtime validators, and type checkers (mypy, Pyright).

## Structural vs nominal

**Nominal:** a value is compatible because its declared type has the right name. Java interfaces and classes work this way.

**Structural:** a value is compatible because it has the right shape. TypeScript is structural:

```ts
type HasEmail = { email: string };

function send(user: HasEmail) { /* ... */ }

send({ email: "a@example.com", name: "Ada" }); // ok
```

Structural typing composes well across libraries; nominal typing gives clearer domain boundaries and prevents accidentally compatible types.

## Inference and generics

Inference lets the compiler derive types from context, so you do not write them everywhere. Generics let one function work over many types while preserving relationships.

```ts
function first<T>(items: T[]): T | undefined {
  return items[0];
}
```

The type parameter `T` preserves the relationship between input and output. Without it, the result would be `unknown` or `any`.

## Soundness and escape hatches

A **sound** type system guarantees that well-typed programs do not get stuck at runtime (for the properties it claims). Real languages trade soundness for practicality:

- TypeScript allows `any` and has unsound array variance.
- Java has null and unchecked casts.
- C# has nullable reference types but also `null!`.
- Rust uses `unsafe` for operations the compiler cannot verify.

Every escape hatch moves risk from compile time to runtime. Use them at boundaries and document why.

## Failure modes

| Smell | Risk | Better approach |
|---|---|---|
| `any` everywhere | Type checking is effectively off | `unknown` plus runtime validation |
| Boolean flags for states | Invalid combinations become representable | Discriminated unions / enums |
| Optional fields for invariants | Callers forget to check | Parse at the boundary into a valid type |
| Casting unvalidated JSON | Runtime shape mismatch | Schema validation (Zod, Pydantic, etc.) |
| Nominal types with primitives | `UserId` and `OrderId` are interchangeable strings | Branded types / value objects |
| Huge union types | Hard to maintain and slow editors | Split by domain and compose |

## Check your understanding

1. Can a language be statically typed and weakly typed? Give a plausible example.
2. Why does TypeScript not guarantee that data from an API matches its declared interface?
3. What does a sound type system guarantee that a test suite does not?

## Primary sources

- TypeScript Handbook, [Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html).
- Pierce, [Types and Programming Languages](https://www.cis.upenn.edu/~bcpierce/tapl/).
- Python, [typing documentation](https://docs.python.org/3/library/typing.html).
- Rust Book, [Generic Types, Traits, and Lifetimes](https://doc.rust-lang.org/book/ch10-00-generics.html).
- Java Language Specification, [Type compatibility](https://docs.oracle.com/javase/specs/).

> **Next:** [SQL vs NoSQL](../databases/sql-vs-nosql.md) applies the same “model the constraints” thinking to data.

---

**Last reviewed: 2026-11-03.** Corrections welcome; open an issue with a primary source.
