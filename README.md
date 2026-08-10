# System-Design Judgement

A personal, living repo for building real system-design judgement — not a
pattern-vocabulary dump, but the ability to choose the right pattern for a set of
constraints, articulate the trade-off, and anticipate failure modes before they
bite.

Patterns here are absorbed *from* the canon (Designing Data-Intensive
Applications, Domain-Driven Design, Microservices Patterns) as tight "what it is /
what it costs / when to use it" notes — backed by hands-on **break-it-then-fix-it**
practicals and design-sparring write-ups.

> Work in progress. The contents below are the syllabus; each item gets filled in
> with notes, diagrams, and practicals as I work through it.

## How this repo is organised

| Path | What lives here |
| --- | --- |
| [`PROPOSAL.md`](PROPOSAL.md) | The full mentor proposal — goal, learning mechanism, measurement, cadence |
| `modules/` | Notes per pattern cluster (one folder per module below) |
| `practicals/` | Hands-on experiments: break a pattern on purpose, then fix it |
| `sparring/` | Design-exercise prompts and my write-ups |
| `rubric.md` | Competency rubric used to score progress *(to add)* |

## The decision framework (the spine)

The lens applied to every pattern below:

- Derive patterns from **NFRs**, never the reverse.
- Complexity must be **pulled** by a stated requirement, not pushed by a pattern.
- Sort decisions by **reversibility** (one-way vs two-way doors); spend effort
  proportionally.
- Separate **"must be correct"** from **"must be available"**; spend the
  complexity budget where correctness matters.
- Name the **coupling** being removed and its permanent cost.

## Contents of learnings

Eight modules, sequenced by leverage. Tick items off as notes + a practical land.

### Module 0 — Foundations: the laws & mental models

- [ ] Latency vs throughput
- [ ] Tail latency (p99/p999) & fan-out amplification
- [ ] Little's Law
- [ ] CAP / PACELC
- [ ] Universal Scalability Law
- [ ] Consistency spectrum (strong, causal, read-your-writes, eventual)
- [ ] Idempotency
- [ ] Backpressure & load shedding

### Module 1 — Boundaries & domain modelling (strategic DDD)

- [ ] Bounded context
- [ ] Ubiquitous language
- [ ] Context mapping (ACL, open-host, published language, conformist, shared kernel)
- [ ] Aggregate / entity / value object
- [ ] Domain event

### Module 2 — Data & storage

- [ ] Replication (leader-follower, multi-leader, leaderless)
- [ ] Partitioning / sharding & hot spots
- [ ] Consistent hashing
- [ ] Quorums (R + W > N)
- [ ] Isolation & concurrency control (optimistic/pessimistic, MVCC)
- [ ] Distributed transactions / 2PC (and why to avoid)

### Module 3 — Communication & decoupling

- [ ] Sync vs async
- [ ] Queue vs log
- [ ] Delivery semantics (at-most / at-least / "exactly" once)
- [ ] Event-driven flavours (notification, state transfer, event sourcing, CQRS)
- [ ] Pub/sub & competing consumers
- [ ] API composition / gateway / BFF

### Module 4 — Consistency across services (the hard part)

- [ ] The dual-write problem
- [ ] Outbox / CDC
- [ ] Saga (orchestration vs choreography) & compensation
- [ ] Idempotent consumer / dedup
- [ ] Leader election / distributed locks

### Module 5 — Performance & scale

- [ ] Caching (cache-aside, write-through/behind), invalidation, stampede
- [ ] CDN / edge
- [ ] Rate limiting (token bucket, leaky bucket)
- [ ] Read replicas & CQRS read models
- [ ] Statelessness + externalised state

### Module 6 — Resilience & failure

- [ ] Timeout + retry with jittered exponential backoff (and retry storms)
- [ ] Circuit breaker
- [ ] Bulkhead
- [ ] Graceful degradation / fallback
- [ ] Redundancy & failover, health checks
- [ ] Chaos engineering

### Module 7 — Delivery & operability (lead-level)

- [ ] Progressive delivery (blue/green, canary, feature flags)
- [ ] Expand-contract migrations
- [ ] Schema evolution / compatibility
- [ ] Observability (RED & USE, SLI / SLO / error budgets)

## Practicals (break it, then fix it)

- [ ] Load-test a small service — watch p50 vs p99 diverge *(Modules 0, 5)*
- [ ] Reproduce the dual-write bug, then fix with an outbox *(Module 4)*
- [ ] Trigger a retry storm, then tame it with backoff + circuit breaker *(Module 6)*
- [ ] Hot-spot a bad partition key on purpose, then re-key *(Module 2)*
- [ ] MIT 6.824 — implement Raft + a sharded KV store *(Modules 0, 2, 4)*
- [ ] Capstone — design *and* build one system end to end; survives a chaos test

## Sources (reference, optional)

Domain-Driven Design (Evans / Vernon) · Designing Data-Intensive Applications
(Kleppmann) · Microservices Patterns (Richardson). Reached for when a distilled
note needs more depth — not a reading list to work through.
