# Concurrency vs parallelism

> Concurrency is dealing with multiple tasks at once; parallelism is executing multiple tasks at the same instant. A single-core machine can be concurrent but not parallel.

**Level:** intermediate  
**Time:** 10 minutes  
**Scope:** the mental model, common coordination primitives, and failure modes. It does not cover lock-free algorithms or memory-model details.

## The mental model

**Concurrency** is a chef switching between several pans while one boils. **Parallelism** is several chefs cooking at the same time. The first is about structuring work; the second is about using multiple execution units.

A program can be concurrent on one CPU through interleaving and context switches. It can be parallel only with multiple CPU cores, threads, processes, or machines.

Where the analogy breaks: the chef chooses when to switch. A scheduler can interrupt a thread at almost any instruction, which is why shared-state reasoning is hard.

## The two families

| Model | Unit | Communication | Strength | Risk |
|---|---|---|---|---|
| Threads | OS threads | Shared memory + locks | Direct, mature | Races, deadlocks, context-switch overhead |
| Processes | OS processes | IPC, sockets | Isolation | Serialization and startup cost |
| Async/event loop | Tasks/coroutines | Message passing or shared state carefully | High I/O concurrency | One blocking call stalls the loop |
| Actors | Actors | Mailboxes | Isolation and backpressure | Mailbox overflow, distributed complexity |
| CSP / channels | Goroutines/tasks | Channels | Explicit handoff | Deadlock, unbuffered-channel stalls |
| Data-parallel | Batches | No shared mutation | Simple scaling | Not every workload splits cleanly |

## A race condition

```text
balance = 100

Thread A: read balance      -> 100
Thread B: read balance      -> 100
Thread A: write balance + 10 -> 110
Thread B: write balance + 20 -> 120

Expected: 130
Actual:   120
```

The interleaving is nondeterministic. Tests may pass for months and fail under load.

### Fixes

- **Mutual exclusion:** a lock around the critical section.
- **Atomic operations:** compare-and-swap or atomic increment.
- **Immutable data:** no shared mutation.
- **Message passing:** one owner serialises updates.
- **Transactions:** the database handles isolation.

Each has a cost. A global lock makes correctness easier and throughput worse.

## Deadlock

A deadlock needs four conditions:

1. Mutual exclusion.
2. Hold and wait.
3. No preemption.
4. Circular wait.

The practical fix is usually to impose a **lock ordering** or to use timeouts and retries. “Just add a lock” can turn a race into a deadlock.

## Async is not automatically parallel

An event loop runs callbacks one at a time. It is excellent for many waiting I/O operations and terrible for CPU-bound work. One synchronous hash computation or image resize blocks everything else.

```text
Event loop:
  task A -> await network
  task B -> await disk
  task C -> CPU-heavy loop  ← stalls A and B
```

Move CPU-heavy work to a worker thread, process, or separate service.

## Backpressure

Concurrency without limits is a denial-of-service attack on yourself. A queue that accepts unbounded work eventually exhausts memory. A thread pool with an unbounded queue hides latency instead of reducing it.

Use bounded queues, concurrency limits, timeouts, and load shedding. Measure queue depth and wait time, not just throughput.

## Failure modes

| Failure | Symptom | Mitigation |
|---|---|---|
| Race condition | Intermittent wrong result | Locks, atomics, immutability, stress tests |
| Deadlock | Requests hang until timeout | Lock ordering, timeouts, avoid nested locks |
| Livelock | Threads retry forever without progress | Randomised backoff, progress limits |
| Starvation | One task never gets scheduled | Fair queues, priority review |
| Thread explosion | Memory and context-switch overhead | Bounded pools, virtual threads |
| Blocked event loop | p99 latency spike under load | Offload CPU work, avoid sync I/O |
| Unbounded queue | Memory growth and stale work | Bounded queues, load shedding |

## Check your understanding

1. Why can a single-threaded JavaScript program be concurrent?
2. A mutex fixes the wrong answer but introduces occasional freezes. What is the likely new bug?
3. You have 10,000 idle network connections. Would you use one thread per connection, an event loop, or a process per connection? Why?

## Primary sources

- Go, [Share Memory By Communicating](https://go.dev/blog/codelab-share).
- MDN, [The event loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop).
- Oracle, [Java concurrency tutorial](https://docs.oracle.com/javase/tutorial/essential/concurrency/).
- Rust Book, [Fearless concurrency](https://doc.rust-lang.org/book/ch16-00-concurrency.html).
- Herlihy and Shavit, [The Art of Multiprocessor Programming](https://www.elsevier.com/books/the-art-of-multiprocessor-programming/herlihy/978-0-12-415950-1).

> **Next:** [Type systems](./type-systems.md) explains how languages constrain the values a program can handle.

---

**Last reviewed: 2026-11-03.** Corrections welcome; open an issue with a primary source.
