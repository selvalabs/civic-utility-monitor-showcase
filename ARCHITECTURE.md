# Architecture

## Design goal

Civic is an information pipeline with resident-facing consequences. The architecture therefore optimizes for four properties:

1. preserve uncertainty from public sources;
2. isolate unstable external formats from stable product rules;
3. make matching and communication deterministic;
4. persist enough state to audit retries, failures, and duplicate prevention.

This document intentionally describes responsibilities and trust boundaries without publishing provider-specific acquisition mechanics or production infrastructure.

## Product flow

![Product flow](diagrams/product-flow.svg)

```text
public notice
→ source adapter
→ normalized notice
→ resident preference and location policy
→ conservative street eligibility
→ persistent notification state
→ external delivery boundary
```

## Runtime responsibilities

![Runtime architecture](diagrams/runtime-architecture.svg)

### Public API

The public application owns resident registration, verification surfaces, authenticated member access, public product pages, and safe health/readiness signals.

It does not mount the internal delivery contracts.

### Worker and source adapters

Collection runs outside the web-process lifecycle. That separation prevents a web reload or additional API process from implicitly multiplying scheduled source work.

Each adapter validates one source contract and emits a common notice model. Missing geographic detail remains missing.

### PostgreSQL

PostgreSQL owns canonical product state, including residents, notices, matches, notifications, and delivery lifecycle records.

Relational uniqueness boundaries make repeated processing safe. The system does not depend on an in-memory “already sent” flag.

### Redis

Redis owns bounded ephemeral state such as opaque sessions, abuse-control counters, and time-limited registration/access artifacts.

It is not the canonical store for resident, notice, or notification history.

### Internal delivery boundary

A separate internal boundary exposes only approved persistent events to an external transport. The product decides eligibility and message semantics; transport owns connectivity and provider credentials.

This separation prevents a messaging integration from becoming the source of product truth.

## Source isolation

Supported public sources differ in format, precision, and lifecycle. The architecture does not force the rest of the system to understand those differences.

```text
source-specific input
        ↓
isolated adapter and validation
        ↓
normalized notice
        ↓
shared matching and notification policy
```

The public showcase omits selectors, acquisition endpoints, polling cadence, and parser implementation.

## Normalized notice model

A normalized notice retains the concepts needed across providers:

- source and notice type;
- stable source identity when available;
- municipality and published street detail;
- source text and normalized description;
- active, last-seen, and resolution state;
- timestamps and diagnostic evidence.

Source-specific lifecycle logic decides what “new,” “still active,” or “resolved” means. Disappearance alone is not promoted into a restoration claim unless the source contract supports it.

## Conservative matching boundary

Matching evaluates a sequence of gates:

1. resident is active and approved;
2. the resident enabled the source;
3. municipality context is compatible;
4. the public notice contains a street;
5. canonical street identity is equivalent;
6. the result and its explanation can be persisted.

Municipality and neighborhood never authorize an individual alert by themselves.

A similarity value may help investigation, but a high fuzzy score does not override a meaningful street-name difference.

## Persistent idempotency

Repeated work is expected:

- a source can repeat the same notice;
- a worker can restart;
- matching can be rerun;
- a delivery attempt can be retried;
- an acknowledgement can arrive after a transient failure.

Civic protects the resident-facing outcome through stable identities and uniqueness boundaries for the relevant lifecycle records. The system verifies final persisted state rather than treating duplicate exceptions as the product contract.

## Notification policy

Notification content is assembled from source-aware application rules and persisted before transport.

The delivery boundary receives an approved message; it does not generate the alert's meaning. Resident templates are deterministic by design because the content can influence real operational decisions.

No autonomous LLM decides:

- whether a notice is sufficiently precise;
- whether a resident is eligible;
- which source facts should be invented or omitted;
- whether a failed delivery should be treated as successful.

## Public and internal trust boundaries

The architecture separates:

- public users from authenticated resident/admin state;
- untrusted provider input from normalized records;
- public routes from internal integration contracts;
- canonical product data from ephemeral session state;
- product semantics from message transport;
- canonical source code from host-specific deployment procedures.

Representative controls include opaque server-side sessions, CSRF protection for mutations, minimized resident data, sanitized failure categories, non-root containers, locked dependencies, and fail-closed production configuration.

Exact credentials, routes, network topology, hosts, and environment structure are intentionally not disclosed here.

## Failure behavior

### Source failure

Unexpected or unavailable source data becomes a source-specific failed run with bounded diagnostic information. It does not silently invent fields.

### Matching uncertainty

Insufficient location precision becomes no individual match.

### Persistence failure

Required database work does not become an apparent success.

### Session or abuse-control failure

Production does not silently bypass a required state/control dependency.

### Delivery failure

Attempts, retry timing, and terminal outcome remain auditable without persisting arbitrary provider error text.

## Deployment model

The deployed system separates the public application, internal delivery boundary, source worker, PostgreSQL, and Redis responsibilities.

The exact VPS, proxy, filesystem, backup, secret-management, and network configuration remain private. Production release evidence is presented in [Engineering evidence](ENGINEERING_EVIDENCE.md) at the revision level only.

## Trade-offs

### Textual identity instead of geospatial intersection

The current product is easier to explain and test but may miss legitimate variations that a geocoded model could resolve.

### Dedicated worker instead of embedding schedules in the web process

This adds an operational role while preventing web lifecycle changes from multiplying collection work.

### Deterministic templates instead of generative resident copy

This limits stylistic flexibility but preserves reproducibility and source meaning.

### Source-specific adapters instead of one universal scraper

This requires maintenance per provider but makes source contracts explicit and isolates failure.

## Deliberately omitted from this public view

- provider endpoints and selectors;
- parsing implementation;
- matching thresholds and complete corpus;
- internal routes and authentication details;
- delivery lease, callback, and retry implementation;
- production host, proxy, paths, backups, and environment files;
- operational logs, source snapshots, and resident records.
