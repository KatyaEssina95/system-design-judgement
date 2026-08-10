# System-Design Judgement

A personal, living repo for getting genuinely good at system design — not just
knowing the names of patterns, but knowing which one to reach for, what it costs,
and how it breaks.

Each pattern gets a short note — what it is, what it costs, and when to use it —
pulled from the main books (Designing Data-Intensive Applications, Domain-Driven
Design, Microservices Patterns). Every note is backed by a hands-on experiment:
break the pattern on purpose, then fix it.

> Work in progress. The list below is the plan; each item gets filled in with
> notes, diagrams, and experiments as I work through it.

## How this repo is organised

| Path | What lives here |
| --- | --- |
| [`PROPOSAL.md`](PROPOSAL.md) | The full plan I'm taking to my mentor — goal, how I'll learn, how we'll measure it, and how often we'll meet |
| `modules/` | Notes, one folder per module below |
| `practicals/` | Hands-on experiments: break a pattern on purpose, then fix it |
| `sparring/` | Design exercises and my write-ups |
| `rubric.md` | A scorecard for tracking progress *(to add)* |

## How I decide

The questions I ask of every pattern below:

- **Start from the real requirements** — traffic, latency, consistency, growth —
  not from a pattern I happen to like.
- **Only add complexity when a requirement demands it**, never because a pattern
  looks clever.
- **Decide fast on things that are easy to undo; think hard about the ones that
  aren't** (one-way vs two-way doors).
- **Know whether a thing must be correct or must be available** — they need
  different solutions — and spend the effort where correctness matters.
- **Before splitting two things apart, be clear what it costs**, because
  decoupling is never free.

## What I'm learning

Eight modules, ordered so the most important ideas come first. Tick items off as
a note and a practical land for each.

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
(Kleppmann) · Microservices Patterns (Richardson). Dip into these when a note
needs more depth — not a reading list to get through.
