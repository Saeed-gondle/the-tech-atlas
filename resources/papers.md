# Papers that changed how systems are built

> Twenty-four papers with a one-line reason to read each. The goal is not to memorise them; it is to borrow their models when you face the same constraint.

## Distributed systems

| Paper | Why it matters | Link |
|---|---|---|
| Dynamo | Consistent hashing, vector clocks, and the original always-write trade-off. | [PDF](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf) |
| The Google File System | Chunked storage and the assumptions behind large-scale file systems. | [Google Research](https://research.google/pubs/pub51/) |
| MapReduce | Data locality, shuffle, and fault-tolerant batch processing. | [Google Research](https://research.google/pubs/pub62/) |
| Bigtable | The wide-column model and tablet-based scaling. | [Google Research](https://research.google/pubs/pub27898/) |
| Spanner | TrueTime and externally consistent global transactions. | [Google Research](https://research.google/pubs/pub39966/) |
| Raft | Understandable consensus and the leader/follower log model. | [PDF](https://raft.github.io/raft.pdf) |
| Kafka | Partitioned logs and consumer groups as a messaging primitive. | [Microsoft Research](https://notes.stephenholiday.com/Kafka.pdf) |
| FoundationDB | Deterministic simulation and a layered transactional core. | [PDF](https://www.foundationdb.org/files/fdb-paper.pdf) |
| Dapper | Low-overhead distributed tracing and trace context. | [Google Research](https://research.google/pubs/pub36356/) |
| Borg | Cluster scheduling and declarative desired state. | [Google Research](https://research.google/pubs/pub43438/) |
| Time, Clocks, and the Ordering of Events | Logical clocks and causality before physical time. | [PDF](https://lamport.azurewebsites.net/pubs/time-clocks.pdf) |
| Life Beyond Distributed Transactions | Why entities and idempotent messages beat global transactions at scale. | [PDF](https://www.ics.uci.edu/~cs223/papers/cidr07p15.pdf) |
| Sagas | Long-lived transactions with compensating actions. | [PDF](https://www.cs.cornell.edu/andru/cs711/2002fa/reading/sagas.pdf) |

## Data and storage

| Paper | Why it matters | Link |
|---|---|---|
| Amazon Aurora | Log-structured storage and separating compute from durability. | [Amazon Science](https://www.amazon.science/publications/amazon-aurora-design-considerations-for-high-throughput-cloud-native-relational-databases) |
| Snowflake | Storage/compute separation and multi-cluster warehouses. | [PDF](https://www.cs.cmu.edu/~15721-f24/papers/Snowflake.pdf) |
| A Comprehensive Study of CRDTs | The convergence model behind local-first collaboration. | [HAL](https://inria.hal.science/inria-00555588/document) |
| HNSW | The graph index behind many vector databases. | [arXiv](https://arxiv.org/abs/1603.09320) |

## AI and machine learning

| Paper | Why it matters | Link |
|---|---|---|
| Attention Is All You Need | The transformer architecture that underpins modern LLMs. | [arXiv](https://arxiv.org/abs/1706.03762) |
| Language Models are Few-Shot Learners | The scaling and prompting behaviour of GPT-3. | [arXiv](https://arxiv.org/abs/2005.14165) |
| Retrieval-Augmented Generation | The original RAG formulation. | [arXiv](https://arxiv.org/abs/2005.11401) |
| ReAct | Interleaving reasoning traces and tool actions. | [arXiv](https://arxiv.org/abs/2210.03629) |
| GPTQ | Post-training 4-bit weight quantisation. | [arXiv](https://arxiv.org/abs/2210.17323) |
| AWQ | Activation-aware weight quantisation. | [arXiv](https://arxiv.org/abs/2306.00978) |
| PagedAttention / vLLM | KV-cache paging for high-throughput LLM serving. | [arXiv](https://arxiv.org/abs/2309.06180) |
| MTEB | A broad benchmark for text embeddings. | [arXiv](https://arxiv.org/abs/2210.07316) |

## Reliability and performance

| Paper | Why it matters | Link |
|---|---|---|
| The Tail at Scale | Why p99 latency is a systems problem, not a single-server problem. | [PDF](https://research.google/pubs/pub40801/) |
| Hidden Technical Debt in Machine Learning Systems | The glue code, pipelines, and feedback loops that dominate ML maintenance. | [NeurIPS](https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems) |
| SRE: How Google Runs Production Systems | SLOs, error budgets, and the human side of reliability. | [Free book](https://sre.google/sre-book/table-of-contents/) |

## How to read a paper

1. **Read the abstract and conclusion first.** Decide whether the problem matters to you.
2. **Find the constraint.** What did the authors assume? What did they give up?
3. **Sketch the architecture.** If you cannot draw it, you have not understood the mechanism.
4. **Read the evaluation.** What did they measure, and what did they omit?
5. **Look for the later critique.** A paper is a snapshot, not the final word.

**A warning:** a paper describes what worked for its authors at a particular scale. It is evidence, not a blueprint. The same design can be wrong for a five-person team.

---

**Last reviewed: 2026-11-03.** Corrections welcome; open an issue with a primary source.
