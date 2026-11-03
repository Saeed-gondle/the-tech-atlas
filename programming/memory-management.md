# Memory management

> Memory management is the set of rules that decide where a program stores data, how long it lives, and who is allowed to free it.

**Level:** beginner → intermediate  
**Time:** 11 minutes  
**Scope:** stack vs heap, manual memory management, garbage collection, and ownership. Examples are conceptual, not a language tutorial.

## The mental model

A restaurant kitchen has two kinds of space:

- **Stack:** a stack of trays by the pass. Fast, ordered, automatically cleared when the order is done.
- **Heap:** the walk-in pantry. Larger and more flexible, but you must track what is yours and when it expires.

A function call pushes a frame onto the stack. Local variables live there and disappear when the function returns. Data whose size or lifetime is not known at compile time usually goes on the heap.

Where the analogy breaks: the stack and heap are memory regions, not physical rooms. A value can be copied between them, and a pointer on the stack can refer to heap data with a longer lifetime.

## Stack

- Allocation is a pointer bump: extremely fast.
- Deallocation is automatic when the frame returns.
- Size is typically fixed at compile time.
- LIFO order means no fragmentation in the usual sense.
- Each thread usually has its own stack.

```text
Call main()
  Call parse()
    push frame: input, buffer, return address
  pop frame
Call render()
  push frame: scene, camera
  pop frame
```

A stack overflow happens when recursion or a huge local allocation exceeds the stack limit.

## Heap

- Allocation is dynamic and may be slower.
- Lifetime is not tied to a single function call.
- Fragmentation is possible.
- Shared across threads, so access needs synchronization.
- The allocator must track free blocks.

```text
Stack                Heap
+--------+           +-------------------+
| ptr    | --------> | object            |
+--------+           |  fields...        |
                     +-------------------+
```

## The three ownership strategies

| Strategy | How it works | Example | Trade-off |
|---|---|---|---|
| Manual | Programmer allocates and frees | C, C++ (raw pointers) | Fast and predictable; use-after-free and leaks |
| Garbage collection | Runtime finds unreachable objects and reclaims them | Java, Go, JavaScript, Python | Safer; pauses, memory overhead, less control |
| Ownership / RAII | Compiler or scope ties deallocation to a value | Rust, C++ RAII | Deterministic cleanup; stricter type rules |

### Garbage collection

A tracing GC starts from roots (globals, stack references, registers) and marks reachable objects. Unmarked objects can be reclaimed.

- **Mark-and-sweep** is simple but can fragment.
- **Copying** collectors move live objects and compact memory.
- **Generational** collectors exploit the fact that most objects die young.
- **Reference counting** frees when the count hits zero, but cycles need a cycle collector.

GC does not mean “no memory bugs.” You can still leak by retaining references in a cache, a listener, or a global map.

### Ownership

Rust's ownership model enforces at compile time:

- Each value has one owner.
- When the owner goes out of scope, the value is dropped.
- Borrowing rules prevent mutable aliases and dangling references.

This gives deterministic cleanup without a GC, at the cost of a steeper learning curve and more explicit lifetime reasoning.

## Common bugs

| Bug | What happens | How to find it |
|---|---|---|
| Use-after-free | Reading memory that was already released | AddressSanitizer, Valgrind, crash traces |
| Double free | Freeing the same allocation twice | Sanitizers, ownership review |
| Memory leak | Unreachable or retained memory grows forever | Heap snapshots, allocation profiles |
| Buffer overflow | Writing past an allocation | Bounds checks, sanitizers |
| Dangling pointer | Pointer outlives the object | Lifetimes, borrow checker, careful APIs |
| Fragmentation | Free memory exists but is not contiguous | Allocator metrics, workload analysis |
| GC pause | Stop-the-world collection | GC logs, p99 latency, heap sizing |

## Practical rules

1. Prefer the smallest lifetime that works.
2. Avoid global caches without an eviction policy.
3. Close files, sockets, and subscriptions in `finally` or a scope guard.
4. Measure before tuning. Heap size and GC choice are workload-specific.
5. In managed languages, retain fewer references; in manual languages, make ownership obvious in the API.

## Check your understanding

1. Why is stack allocation usually faster than heap allocation?
2. A Java service has stable memory usage but increasing latency. How could GC still be the cause?
3. What does Rust's borrow checker prevent that a tracing GC does not?

## Primary sources

- MDN, [Memory management](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Memory_management).
- Rust Book, [Ownership](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html).
- V8, [Memory management](https://v8.dev/blog/trash-talk).
- Oracle, [HotSpot garbage collection tuning](https://docs.oracle.com/en/java/javase/21/gctuning/).
- Jones, Hosking, and Moss, [The Garbage Collection Handbook](https://gchandbook.org/).

> **Next:** [Concurrency vs parallelism](./concurrency.md) explains what happens when multiple tasks share memory and time.

---

**Last reviewed: 2026-11-03.** Corrections welcome; open an issue with a primary source.
