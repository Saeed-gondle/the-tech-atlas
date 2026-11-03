# CI/CD

> Continuous integration (CI) automatically builds and tests changes; continuous delivery/deployment (CD) automatically promotes a verified artifact to an environment. The goal is a short, repeatable path from commit to production.

**Level:** beginner  
**Time:** 10 minutes  
**Scope:** pipeline design, artifacts, environments, and deployment strategies. It does not cover every CI vendor or Kubernetes operator.

## The mental model

An assembly line. Every raw commit enters the same stations: lint, test, build, scan, package, deploy. A failure stops the line before a bad part reaches the customer. A good line produces the **same artifact** every time, not a fresh build for each environment.

Where the analogy breaks: software can be deployed incrementally and rolled back. The pipeline also has to handle secrets, approvals, and data migrations.

## The pipeline

```mermaid
flowchart LR
    C[Commit / PR] --> L[Lint + typecheck]
    L --> T[Unit tests]
    T --> B[Build artifact]
    B --> S[Security scan]
    S --> I[Integration tests]
    I --> P[Publish image/package]
    P --> ST[Staging deploy]
    ST --> A[Approval / canary]
    A --> PR[Production deploy]
    PR --> M[Monitor + rollback]
```

### Build once, deploy many

Build an immutable artifact (container image, binary, bundle) once. Promote the **same digest** through staging and production. Rebuilding per environment invites “works in staging” drift.

## Pipeline principles

| Principle | Why | Failure if ignored |
|---|---|---|
| Fast feedback | Developers fix small problems | Slow CI becomes ignored CI |
| Deterministic builds | Same input → same output | “Works on the CI runner” |
| Hermetic builds | No hidden network dependencies | Supply-chain surprises |
| Least-privilege secrets | CI is a high-value target | Token theft and lateral movement |
| Fail closed | Security checks matter | Warnings become permanent noise |
| Reproducible migrations | Schema changes are risky | Data loss and rollback failures |

## Testing layers

| Layer | What it catches | Speed | Cost |
|---|---|---|---|
| Unit | Logic errors | Fast | Low |
| Integration | Contract and database issues | Medium | Medium |
| End-to-end | User flows | Slow | High |
| Smoke | Deployment basics | Fast | Low |
| Synthetic monitoring | Real-world availability | Continuous | Medium |

Do not replace unit tests with a giant end-to-end suite. Do not rely only on unit tests for a payment flow.

## Deployment strategies

| Strategy | How | Strength | Risk |
|---|---|---|---|
| Rolling | Replace instances gradually | Simple, no extra fleet | Mixed versions during rollout |
| Blue/green | Switch traffic between two environments | Fast rollback | Double capacity |
| Canary | Send a small percentage to the new version | Real-world signal | Requires good metrics |
| Feature flag | Deploy dark, enable gradually | Decouples deploy from release | Flag debt |
| Shadow | Mirror traffic to new version | Safe comparison | Duplicate side effects |

**Rollback is a feature.** A deploy is not complete until you know how to reverse it.

## Supply chain

- Pin action versions or container digests.
- Generate an SBOM.
- Sign artifacts (Sigstore/cosign).
- Verify provenance (SLSA).
- Scan dependencies and images.
- Use short-lived OIDC credentials instead of long-lived cloud keys.
- Require review for workflow changes; CI config is code execution.

## Failure modes

| Symptom | Cause | Fix |
|---|---|---|
| CI is slow | No caching, serial jobs | Parallelise, cache dependencies |
| Flaky tests | Time, network, shared state | Isolate, retry with data, fix root cause |
| Secrets leaked | Echoed env or PR from fork | Mask, restrict, rotate, use OIDC |
| Production differs from staging | Rebuilt artifact or config drift | Build once, same digest, config as code |
| Migration blocks deploy | Irreversible schema change | Expand/contract, backward-compatible steps |
| Rollback fails | Data migrated forward | Design rollback and backups first |
| Pipeline is a single point of failure | One provider, no fallback | Mirror critical workflows, document recovery |

## Check your understanding

1. Why should you promote the same artifact rather than rebuild for production?
2. What is the difference between continuous delivery and continuous deployment?
3. A canary looks healthy but users report errors. What metrics might be missing?

## Primary sources

- GitHub, [Actions documentation](https://docs.github.com/en/actions).
- Google, [DORA / State of DevOps](https://dora.dev/).
- OpenSSF, [SLSA supply-chain levels](https://slsa.dev/).
- NIST, [SP 800-204D: CI/CD security](https://csrc.nist.gov/pubs/sp/800/204/d/final).
- Martin Fowler, [Blue-green deployment](https://martinfowler.com/bliki/BlueGreenDeployment.html).

> **Next:** [Big-O notation](../computer-science/big-o.md) gives you the vocabulary to reason about the algorithms inside the pipeline.

---

**Last reviewed: 2026-11-03.** Corrections welcome; open an issue with a primary source.
