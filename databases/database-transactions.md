# Database transactions

> A transaction groups multiple reads and writes into one logical unit that either commits as a whole or aborts, while isolation rules control what concurrent transactions can observe.

**Level:** intermediate  
**Time:** 10 minutes  
**Scope:** ACID, isolation levels, anomalies, MVCC, and distributed transaction basics. It does not cover every database's implementation details.

## The mental model

A bank transfer is two updates: subtract from Alice, add to Bob. If the process crashes between them, money disappears. A transaction makes the pair atomic: both happen or neither does.

**Atomicity** is the “all or nothing” promise. **Consistency** means the database moves from one valid state to another, according to constraints. **Isolation** controls interference between concurrent transactions. **Durability** means a committed change survives a crash.

Where the analogy breaks: consistency in ACID is about declared invariants, not business correctness. A transaction can be atomic and still encode a bad rule.

## ACID, precisely

| Property | Guarantee | Common misconception |
|---|---|---|
| Atomicity | All writes commit or none do | “It prevents all bugs” |
| Consistency | Constraints and invariants hold | “It validates business logic automatically” |
| Isolation | Concurrent transactions behave according to a level | “Serializable is the default” |
| Durability | Committed data survives crashes | “It replaces backups” |

## Isolation levels and anomalies

| Level | Dirty read | Non-repeatable read | Phantom read | Write skew |
|---|---|---|---|---|
| Read uncommitted | Possible | Possible | Possible | Possible |
| Read committed | Prevented | Possible | Possible | Possible |
| Repeatable read | Prevented | Prevented | Possible / prevented* | Possible |
| Serializable | Prevented | Prevented | Prevented | Prevented |

\*Postgres's repeatable read prevents phantoms in many cases but is not fully serializable. Always check the actual engine's semantics.

**Write skew** is the classic subtle anomaly: two transactions read the same condition, each writes a different row, and the combined result violates the invariant. A row lock does not necessarily stop it; serializable isolation or explicit locking does.

## MVCC

Multi-version concurrency control keeps multiple versions of a row so readers do not block writers and writers do not block readers. Each transaction sees a snapshot.

```mermaid
sequenceDiagram
    participant T1 as Transaction A
    participant DB as MVCC store
    participant T2 as Transaction B
    T1->>DB: BEGIN; read row v1
    T2->>DB: BEGIN; write row v2; COMMIT
    T1->>DB: read row again
    DB-->>T1: v1 (snapshot isolation)
    T1->>DB: COMMIT
```

The cost is version storage and cleanup. Postgres uses `VACUUM`; other engines use different garbage-collection strategies. Long-running transactions can hold back cleanup and cause bloat.

## Distributed transactions

When data spans services or databases, atomic commit is harder. Options:

- **Two-phase commit (2PC):** a coordinator asks all participants to prepare, then commit. It can block if the coordinator fails.
- **Saga:** a sequence of local transactions with compensating actions. It is not atomic; it is eventually consistent.
- **Outbox pattern:** write the business row and an event row in one local transaction; a relay publishes the event.
- **Idempotency keys:** make retries safe when the network is ambiguous.

```text
Order service: create order + outbox event (one transaction)
Relay: publish event to queue
Payment service: consume event, charge, publish result
```

The outbox pattern solves the “database committed but message not sent” gap without a distributed transaction.

## Isolation in practice

- **Postgres** defaults to read committed. Use `SELECT ... FOR UPDATE` for row-level coordination, or serializable with retry logic.
- **MySQL/InnoDB** defaults to repeatable read.
- **SQLite** serializes writes; readers see a consistent snapshot in WAL mode.
- **MongoDB** supports multi-document transactions, but they have operational costs.

Do not assume a level; read the docs and test the anomaly you care about.

## Failure modes

| Failure | Symptom | Mitigation |
|---|---|---|
| Lost update | Two writers overwrite each other | Atomic update, version column, row lock |
| Write skew | Invariant violated despite serial-looking writes | Serializable, constraint, explicit lock |
| Long transaction | Bloat, lock contention, replication lag | Keep transactions short; batch outside |
| Deadlock | Transactions wait on each other | Consistent lock order; retry |
| Partial distributed write | Order created, payment not | Outbox, saga, idempotency |
| Duplicate retry | Double charge | Idempotency key + unique constraint |

## Check your understanding

1. Why does read committed allow a non-repeatable read?
2. What is the difference between atomicity and durability?
3. A payment service times out after charging the card. What makes the retry safe?

## Primary sources

- PostgreSQL, [Transactions and isolation](https://www.postgresql.org/docs/current/transaction-iso.html).
- PostgreSQL, [MVCC](https://www.postgresql.org/docs/current/mvcc.html).
- Kleppmann, [Designing Data-Intensive Applications](https://dataintensive.net/).
- Gray and Reuter, [Transaction Processing](https://www.elsevier.com/books/transaction-processing/gray/978-1-55860-190-1).
- Jepsen, [Consistency models](https://jepsen.io/consistency).

> **Next:** [Replication](./replication.md) explains what happens when the database is copied.

---

**Last reviewed: 2026-11-03.** Corrections welcome; open an issue with a primary source.
