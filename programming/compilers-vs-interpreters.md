# Compilers vs interpreters

> A compiler translates a program into another form before it runs; an interpreter executes the program's instructions directly. Most modern runtimes blend both approaches.

**Level:** beginner  
**Time:** 9 minutes  
**Scope:** the translation pipeline and the compiler/interpreter spectrum. It does not teach parser theory or optimisation passes in depth.

## The mental model

A compiler is a translator producing a book in another language. An interpreter is a live interpreter translating sentence by sentence as the speaker talks. A **just-in-time (JIT) compiler** is a translator who starts live, notices which phrases are repeated, and pre-translates those hot passages.

Where the analogy breaks: the boundary is not a property of a language. Python can be compiled to bytecode; JavaScript engines compile hot code to machine code; C can be interpreted by a C interpreter.

## The front end

Most language implementations share a front end:

```mermaid
flowchart LR
    S[Source code] --> L[Lexer]
    L --> T[Tokens]
    T --> P[Parser]
    P --> A[AST]
    A --> Sema[Semantic analysis]
    Sema --> IR[Intermediate representation]
    IR --> O[Optimiser]
    O --> B[Backend / bytecode]
```

1. **Lexing** turns characters into tokens.
2. **Parsing** builds an abstract syntax tree (AST) and rejects invalid grammar.
3. **Semantic analysis** resolves names, checks types, and enforces rules.
4. **IR generation** lowers the program to a simpler representation.
5. **Optimisation** removes redundant work.
6. **Backend** emits machine code, bytecode, or another language.

The front end is where syntax errors and many type errors are found. The back end decides what actually runs.

## The execution spectrum

| Strategy | Translation time | Runtime speed | Portability | Example |
|---|---|---|---|---|
| Pure interpreter | None / per statement | Slow | High | A simple AST walker |
| Bytecode VM | Compile once to bytecode | Moderate | High | CPython, JVM bytecode |
| JIT | Compile hot code at runtime | Fast | Medium | V8, HotSpot |
| Ahead-of-time compiler | Before execution | Fast | Low (per target) | C, Rust, Go |
| Transpiler | To another high-level language | Depends on target | High | TypeScript → JavaScript |

### Bytecode

A bytecode VM compiles source to compact instructions for a virtual machine. It is easier to port than machine code and faster than walking an AST repeatedly. The JVM and CPython both use bytecode, but the JVM adds sophisticated JIT compilation while CPython's default implementation remains largely interpretive.

### JIT compilation

A JIT watches execution, identifies hot functions, compiles them to machine code, and may deoptimise if assumptions break. It can use runtime type information that an ahead-of-time compiler does not have. The cost is warm-up time, memory, and complexity.

```text
cold code → interpreter → profile → hot code → JIT → machine code
                                      ↓
                              assumption fails
                                      ↓
                                deoptimise
```

## Why the distinction matters

- **Startup:** interpreters and bytecode VMs often start faster; AOT binaries avoid warm-up.
- **Peak performance:** JITs can beat static compilers on dynamic workloads; AOT wins on predictable latency.
- **Deployment:** bytecode is portable; native binaries are platform-specific.
- **Debugging:** source maps and bytecode make stack traces harder to map.
- **Security:** JIT engines have a large attack surface; W^X and sandboxing matter.

## Failure modes

| Symptom | Likely cause | What to inspect |
|---|---|---|
| Slow first request, fast later | JIT warm-up | Profile after warm-up; consider tiered compilation |
| Occasional latency spike | Deoptimisation or GC | JIT logs, GC logs |
| Different behaviour across runtimes | Undefined behaviour or version differences | Language spec and runtime version |
| Huge binary | AOT with static linking | Strip symbols, dead-code elimination |
| Source maps wrong | Stale build artefacts | Rebuild and verify map paths |

## Check your understanding

1. Why can JavaScript be both interpreted and compiled?
2. What does a JIT know at runtime that an ahead-of-time compiler may not?
3. A service is fast in benchmarks but slow on the first request after deploy. What is the likely cause?

## Primary sources

- Nystrom, [Crafting Interpreters](https://craftinginterpreters.com/).
- LLVM, [Kaleidoscope tutorial](https://llvm.org/docs/tutorial/).
- V8, [Ignition and TurboFan](https://v8.dev/blog/ignition-interpreter).
- ECMAScript, [Language specification](https://tc39.es/ecma262/).
- Aho, Lam, Sethi, and Ullman, [Compilers: Principles, Techniques, and Tools](https://www.pearson.com/en-us/subject-catalog/p/compilers-principles-techniques-and-tools/P200000003484).

> **Next:** [Memory management](./memory-management.md) explains where the data lives while the program runs.

---

**Last reviewed: 2026-11-03.** Corrections welcome; open an issue with a primary source.
