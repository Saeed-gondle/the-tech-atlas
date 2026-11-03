# SQL vs NoSQL

> SQL databases model data as relations with a schema and query it with a declarative language; NoSQL is an umbrella for non-relational stores that optimise specific access patterns, usually by giving up some relational guarantees.

**Level:** beginner  
**Time:** 10 minutes  
**Scope:** the practical choice between relational and non-relational data models. It does not claim that one category is universally better.

## The mental model

A relational database is a set of normalized tables with foreign keys and a query planner. You describe **what** you want; the database decides how to fetch it.

A NoSQL store is usually designed around **how** you will access the data. A document store keeps a whole object together; a key-value store is a giant dictionary; a wide-column store is optimised for known query patterns at scale.

Where the analogy breaks: modern Postgres supports JSON, full-text search, and vector indexes; modern document databases support transactions and joins. The labels are marketing categories, not hard boundaries.

## The relational model

Tables, rows, columns, primary keys, foreign keys. Relationships are explicit. The schema is enforced by the database.

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE orders (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES users(id),
  total_cents INTEGER NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

SQL gives you joins, aggregations, constraints, transactions, and a planner that can use indexes. It is excellent when the access patterns are still changing and integrity matters.

## The NoSQL families

| Family | Data model | Best for | Weak point |
|---|---|---|---|
| Key-value | `key → value` | Caches, sessions, simple lookups | No rich queries |
| Document | JSON-like documents | Aggregate-oriented objects, flexible fields | Cross-document joins and integrity |
| Wide-column | Rows with dynamic columns | High-write time-series and messaging at scale | Query-first schema design |
| Graph | Nodes and edges | Relationship traversal, fraud, recommendations | Operational complexity and query tuning |
| Search engine | Inverted index | Full-text and relevance ranking | Not a primary system of record |
| Time-series | Timestamped metrics | Metrics, IoT, observability | Not general-purpose transactions |

## The real decision

Ask these questions in order:

1. **What are the invariants?** If a foreign key must always exist and money must balance, a relational database with constraints is a strong default.
2. **What are the access patterns?** If you know them and they are aggregate-oriented, a document store can reduce joins.
3. **What is the write scale?** A single Postgres instance handles more than most products need. Measure before assuming you need a distributed store.
4. **What consistency do you need?** Read-your-writes, linearizability, and cross-entity transactions are not free.
5. **Who operates it?** A managed Postgres is often cheaper than a self-run distributed system with a smaller team.

## A pragmatic default

For most SaaS products, start with **Postgres**:

- relational integrity,
- transactions,
- JSONB for flexible fields,
- full-text search,
- `pgvector` for embeddings,
- mature backups and tooling.

Move a specific workload out when you have evidence: a search index for relevance, a cache for latency, an object store for blobs, a time-series store for metrics. Do not move the whole product because a blog post said “web scale.”

## Trade-offs

| Concern | Relational | Document / key-value |
|---|---|---|
| Schema change | Migration required | Flexible but easy to drift |
| Joins | Native | Often application-side or denormalized |
| Transactions | Strong, multi-row | Varies; often single-document |
| Query flexibility | High with SQL | Limited by index design |
| Horizontal writes | Harder, but possible | Often built for partitioning |
| Integrity | Database-enforced | Application-enforced |
| Operational maturity | Very high | Varies by product |

## Failure modes

| Failure | Cause | Fix |
|---|---|---|
| N+1 queries | ORM lazy loading | Eager loading, query review |
| Unindexed scan | Missing or unused index | `EXPLAIN`, index the predicate |
| Schema drift | Ad-hoc JSON fields | Validation and versioned migrations |
| Hot partition | Poor partition key | Rework key or add write sharding |
| Lost updates | Read-modify-write without a transaction | Atomic update, optimistic concurrency |
| Denormalization inconsistency | Duplicated fields | Events, reconciliation, or normalize |

## Check your understanding

1. Why is “NoSQL scales better” an incomplete claim?
2. You need a multi-tenant SaaS with billing invariants. What would make you choose Postgres over a document store?
3. When is a search engine the right secondary store rather than the primary database?

## Primary sources

- PostgreSQL, [Data definition and constraints](https://www.postgresql.org/docs/current/ddl.html).
- MongoDB, [Data modeling](https://www.mongodb.com/docs/manual/data-modeling/).
- Sadalage and Fowler, [NoSQL Distilled](https://martinfowler.com/books/nosql.html).
- Kleppmann, [Designing Data-Intensive Applications](https://dataintensive.net/).
- Jepsen, [Consistency models](https://jepsen.io/consistency).

> **Next:** [Database indexing](./database-indexing.md) explains how a query stops scanning every row.

---

**Last reviewed: 2026-11-03.** Corrections welcome; open an issue with a primary source.
