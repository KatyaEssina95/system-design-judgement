# Proposal: Building System-Design Judgement

A structured, mentor-supported plan to move from *not-yet-knowing* the core
system-design patterns to *exercising judgement* with them — choosing the right
pattern for a set of constraints, articulating the trade-off, and anticipating
failure modes before they bite.

## Goal

Success is not reciting a pattern but: when the next greenfield system lands, do
the boundaries and key decisions hold up under load, failure, and change.

## How the learning happens (no book-reading required)

Patterns are absorbed *from* the canon (DDIA, Domain-Driven Design, Microservices
Patterns) without reading it cover to cover:

- **On-demand distillation** — each pattern is taught as a tight "what it is /
  what it costs / when to use it," not a chapter.
- **Design sparring** — apply the pattern immediately against a realistic prompt;
  defend the choice.
- **Break-it practicals** — feel the consequence of getting it wrong, then fix it.

Books stay in an appendix as reference for anything worth going deeper on later —
optional, never a milestone.

## The decision framework (the spine — runs through every module)

- Derive patterns from NFRs, never the reverse — read/write ratio, latency budget
  at a named percentile, consistency need, tolerable data-loss window, growth,
  team/ops maturity.
- Complexity must be *pulled* by a stated requirement, not *pushed* by a pattern.
- Sort decisions by reversibility (one-way vs two-way doors); spend design effort
  proportionally.
- Separate "must be correct" from "must be available"; put the complexity budget
  where correctness matters.
- Name the coupling being removed and its permanent cost.

## The pattern catalogue (the syllabus)

Grouped into eight modules, sequenced by leverage. Each line is the one-sentence
definition so the scope is self-contained.

### Module 0 — Foundations: the laws & mental models

- **Latency vs throughput** — speed of one request vs volume per second; you
  optimise one at a time.
- **Tail latency (p99/p999) & fan-out amplification** — the slowest requests
  define user experience once one request hits many services.
- **Little's Law** — concurrency = arrival-rate × time-in-system; sizes pools,
  threads, queues.
- **CAP / PACELC** — under a partition, consistency *or* availability; *else*,
  latency *or* consistency in the normal case.
- **Universal Scalability Law** — contention and coordination mean throughput can
  *fall* as you add nodes.
- **Consistency spectrum** — strong, causal, read-your-writes, eventual — and what
  each costs.
- **Idempotency** — same operation applied twice has the same effect; the price of
  retries.
- **Backpressure & load shedding** — how a system says "slow down" instead of
  collapsing.

### Module 1 — Boundaries & domain modelling (strategic DDD)

- **Bounded context** — an explicit boundary within which a model and its terms
  mean one thing.
- **Ubiquitous language** — one shared vocabulary between code and domain experts,
  per context.
- **Context mapping** — how contexts relate: partnership, customer/supplier,
  conformist, **anti-corruption layer** (translate an upstream model so it can't
  leak in), open-host service, published language, shared kernel.
- **Aggregate / entity / value object** — the unit of consistency, a thing with
  identity, a thing defined only by its values.
- **Domain event** — a record that something meaningful happened in the domain.

### Module 2 — Data & storage

- **Replication** — leader-follower, multi-leader, leaderless; copies for
  availability and read scale.
- **Partitioning / sharding** — splitting data across nodes; choosing a key that
  avoids **hot spots**.
- **Consistent hashing** — add/remove nodes without reshuffling everything.
- **Quorums (R + W > N)** — read/write overlap for consistency in leaderless
  systems.
- **Isolation & concurrency control** — optimistic vs pessimistic locking, MVCC,
  what each isolation level actually prevents.
- **Distributed transactions / 2PC** — atomic writes across systems, and why
  they're usually the wrong tool.

### Module 3 — Communication & decoupling

- **Sync vs async** — request/response coupling vs messaging; what each buys and
  costs.
- **Queue vs log** — work-to-be-done vs a replayable ordered record (Kafka-style).
- **Delivery semantics** — at-most-once, at-least-once, "exactly-once"; why
  at-least-once + idempotency is the realistic target.
- **Event-driven flavours** — **event notification** (thin "it happened"),
  **event-carried state transfer** (event includes the data so consumers don't
  call back), **event sourcing** (state *is* the event log), **CQRS** (separate the
  write model from purpose-built read models).
- **Pub/sub & competing consumers** — fan-out to many, or share work across a pool.
- **API composition / gateway / BFF** — aggregate across services; a single edge;
  a per-client backend.

### Module 4 — Consistency across services (the hard part)

- **The dual-write problem** — writing to DB *and* publishing a message can't be
  atomic naively; one can fail.
- **Outbox / CDC** — commit the event in the same transaction as the state,
  publish it reliably afterwards.
- **Saga (orchestration vs choreography)** — a multi-step workflow across services
  with **compensating actions** instead of a distributed transaction.
- **Idempotent consumer / dedup** — safely handle the duplicates at-least-once
  delivery guarantees you'll get.
- **Leader election / distributed locks** — single-writer coordination, and why
  it's a footgun.

### Module 5 — Performance & scale

- **Caching** — cache-aside, read/write-through, write-behind; **invalidation**,
  TTLs, **stampede/thundering-herd**.
- **CDN / edge** — serve close to the user.
- **Rate limiting** — token bucket, leaky bucket; protect yourself and be a good
  neighbour.
- **Read replicas & CQRS read models** — scale reads separately from writes.
- **Statelessness + externalised state** — the enabler for horizontal autoscaling.

### Module 6 — Resilience & failure

- **Timeout + retry with jittered exponential backoff** — and how naive retries
  cause **retry storms**.
- **Circuit breaker** — stop hammering a failing dependency.
- **Bulkhead** — isolate resource pools so one failure can't sink the ship.
- **Graceful degradation / fallback** — a partial or stale answer beats an error.
- **Redundancy & failover** — replication for availability, health checks,
  readiness vs liveness.
- **Chaos engineering** — inject failure on purpose to prove resilience.

### Module 7 — Delivery & operability (lead-level)

- **Progressive delivery** — blue/green, canary, feature flags.
- **Expand-contract migrations** — change schemas without downtime or breaking
  readers.
- **Schema evolution / compatibility** — backward/forward-compatible events and
  APIs.
- **Observability** — metrics/logs/traces, RED & USE methods,
  **SLI / SLO / error budgets**.

## Practical component (break it, then fix it)

- **Load-test a small service** (k6/Locust) — watch p50 vs p99 diverge; make
  Little's Law and tail latency real. *(Modules 0, 5)*
- **Reproduce the dual-write bug** — write-then-publish, no outbox, kill
  mid-flight, observe permanent inconsistency; add the outbox and feel the fix.
  *(Module 4)*
- **Trigger a retry storm** — naive retries + injected latency (Toxiproxy); then
  jittered backoff + circuit breaker holds it. *(Module 6)*
- **Shard something and hot-spot it on purpose** — pick a bad partition key, watch
  one node melt; re-key it. *(Module 2)*
- **MIT 6.824 labs** — implement Raft + a sharded KV store; the highest-value
  single item for *feeling* consistency and consensus. *(Modules 0, 2, 4)*
- **Design sparring (every session)** — mentor sets a fuzzy-requirements prompt; I
  extract NFRs, propose a design, defend trade-offs; mentor plays adversary on
  failure modes until it holds.
- **Capstone** — design *and build* one non-trivial system end to end: written
  design doc, load-test evidence, survives a defined chaos test.

## How we measure success

### Leading indicators (per session)

- I state NFRs *before* proposing a design, unprompted.
- Every pattern I introduce is tied to the specific requirement pulling it in.
- I flag one-way vs two-way doors and spend effort accordingly.
- I name a design's failure modes before the mentor does — this ratio climbs over
  time.

### Lagging indicators (the proof)

- **Competency rubric**, co-scored at the start and every ~6 weeks across five
  dimensions, each rated *aware / apply-with-guidance / independent / can-teach*:
  1. NFR elicitation & framing
  2. Boundary / context design
  3. Trade-off articulation (complexity vs correctness, reversibility)
  4. Failure-mode anticipation & resilience
  5. Data & consistency modelling
- **Artifacts** — a growing set of ADRs / design docs reviewed against the
  framework; visibly improving trade-off sections.
- **Capstone bar** — the built system survives a defined failure injection and
  meets a stated latency budget under load; pass/fail, evidenced.
- **Transfer test** — I run the design review for a real upcoming project;
  boundaries and key decisions survive the mentor's adversarial review with no
  major gaps.
- **Teaching signal (top of ladder)** — I can run a sparring session *for someone
  else*, explaining not just the choice but why the alternatives lose.

## Cadence & what I need from my mentor

- Suggested **fortnightly 60–90 min** — half sparring, half a pattern-module
  deep-dive + its practical.
- Mentor's role: set prompts, play adversary on failure modes, co-score the
  rubric, pressure-test my ADRs.
- Rough **shape (negotiable):** Modules 0–1 (framework + boundaries) weeks 1–4 →
  Modules 2–4 (data, comms, cross-service consistency) + core practicals weeks
  5–14 → Modules 5–6 (scale, resilience) weeks 15–20 → Module 7 + capstone weeks
  21–26, rubric re-scored throughout.

## Appendix — sources (optional, not part of the plan)

Domain-Driven Design (Evans/Vernon) for Modules 0–1; Designing Data-Intensive
Applications (Kleppmann) for Modules 2–4; Microservices Patterns (Richardson) for
Modules 3–4, 7. Reference only — reach for a chapter when you want depth beyond
the distilled version.
