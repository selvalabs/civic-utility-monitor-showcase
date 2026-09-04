# Engineering evidence

## Evidence policy

Civic's public claims are tied to an observed test or release boundary. A passing local suite does not prove live-source availability. A production smoke test does not prove guaranteed delivery. A deployed application does not prove scale or an SLA.

This case study therefore separates deterministic validation, external compatibility, and production verification.

## Baselines

| Reference | Meaning |
| --- | --- |
| `82dc06d` | Production runtime revision verified after the final public-readiness release |
| `adeb784` | Repository evidence revision; a direct descendant that changed only the production-smoke contract and its report |

The later evidence revision did not change runtime code, so this showcase does not claim that production runs `adeb784`.

The canonical repository is private; the identifiers are retained here to make the evidence chain precise rather than presented as public source links.

## Validation layers

### Deterministic suite

At the matching-remediation certification boundary:

```text
367 collected
353 passed
0 failed
14 explicit opt-in checks skipped in the ordinary run
```

The opt-in group separated live sources, production smoke, and environment-specific integration checks from the default deterministic run.

### Browser readiness

Twelve Chromium checks passed at certification, covering public and member-facing boundaries, localization behavior, and representative desktop/mobile states.

A later public-content release separately reran ten browser/i18n checks after updating the live Terms, Privacy, and project surfaces.

### PostgreSQL and Redis

Disposable PostgreSQL 16 and Redis 7 environments were used for migration, persistence, idempotency, outbox, session, and integration contracts. Temporary services and data were removed after the runs.

SQLite-only success was not treated as sufficient evidence for PostgreSQL-dependent behavior.

### Production smoke

After deployment, nine read-only checks validated public routes, health/readiness, navigation, localization surfaces, and the public/internal route boundary.

The smoke suite intentionally did not create, edit, message, or delete a production resident.

### Live-source compatibility

Explicit read-only checks passed for:

- Celesc scheduled outage source;
- Celesc emergency source;
- CASAN notice source.

A live-source pass means the public source remained reachable and compatible with the current adapter contract at the time checked. It is not a provider SLA.

## Case study: PRD-001

### Product invariant

An individual alert requires a sufficiently precise match to the resident's registered street. A municipality or neighborhood alone is never enough.

### Failure found by readiness testing

The adversarial matrix included:

```text
registered: Rua das Flores
published:  Rua das Flores do Sul
```

The baseline incorrectly produced a street match and resident impact.

### Root cause

The street normalizer preserved the meaningful suffix correctly. The defect was the eligibility policy around a generic token-set similarity score: a strict token subset could receive a score of 100.

The number looked confident while the product result was wrong.

### Remediation

The correction separated street eligibility from generic fuzzy scoring:

- representational variants may canonicalize;
- any meaningful remaining identity difference fails closed;
- similarity may be retained for audit context;
- similarity alone cannot authorize an individual alert;
- municipality behavior remained unchanged.

No hardcoded street exception was introduced.

### Regression evidence

The positive matrix preserved equivalent representations, including abbreviations, accents, case, punctuation, and spacing.

The negative matrix covered directional suffixes, additional proper-name tokens, numeric differences, Roman numerals, different street types, and materially different names.

At the value-loop level:

```text
positive case
→ one match
→ one notification
→ one delivery event

negative case
→ no match
→ no notification
→ no delivery event
```

The negative result was verified with PostgreSQL persistence, not only an isolated comparator.

### Fault-sensitivity evidence

A temporary disposable mutation restored the unsafe subset-friendly behavior. The direct street cases and negative value-loop tests turned red. The mutation was reverted and did not enter the committed candidate.

This mattered because green tests are stronger evidence when a controlled defect can demonstrably make them fail.

### Promotion

The corrected candidate then passed:

- diff review;
- formatting and static analysis;
- full deterministic suite;
- browser readiness;
- PostgreSQL and Redis contracts;
- security and dependency checks;
- remote CI and repository-hygiene gates.

It was merged through the normal pull-request process, deployed as an immutable revision, and verified through checkout and image identity.

## Controlled production release

The release chain was:

```text
certified candidate
→ reviewed pull request
→ applicable CI and security gates
→ merged main revision
→ pre-deploy backup and rollback checkpoint
→ exact revision build
→ service restart
→ deployed revision proof
→ health and readiness
→ production smoke
→ live-source compatibility
```

Production verification confirmed the public API, internal API, worker, PostgreSQL, and Redis responsibilities were healthy. No database migration was required for the public-readiness release.

No destructive PRD-001 replay was performed against real production data. The production proof combines deterministic regression evidence with exact deployed-revision identity.

## Security and quality controls

| Control | Purpose | Result at relevant release boundary |
| --- | --- | --- |
| Ruff and formatting | Code quality and consistent formatting | Passed |
| Mypy | Static type checking | Passed |
| Pytest | Product, contract, readiness, and browser behavior | Passed |
| Bandit | Python security linting | Passed |
| pip-audit | Locked dependency vulnerability audit | Passed |
| Gitleaks | Repository-history secret scan | Passed |
| Repository hygiene | Reject runtime data, unsafe artifacts, and obvious secret/path patterns | Passed |
| Container checks | Build, non-root runtime, Compose and health smoke | Passed |
| PostgreSQL/Redis contracts | Production-store behavior | Passed |

GitHub-hosted CodeQL is not an active gate for the private canonical repository under its current repository entitlement. It is reported as unavailable/N/A, not as a pass.

## Git and GitHub workflow

The project used a visible engineering lifecycle even though the canonical repository remains private:

```text
scope and invariant
→ focused branch
→ implementation
→ deterministic validation
→ readiness/audit
→ pull request
→ CI and security gates
→ merge
→ controlled deployment
→ production verification
```

Two representative events were the matching-safety remediation and the later public-content truthfulness remediation. Private PR links are intentionally not used as public evidence because they would be inaccessible to a recruiter.

## What the evidence does not prove

The showcase does not claim:

- guaranteed email or WhatsApp delivery;
- live delivery-rate metrics;
- Redis TTL passage under every production timing condition;
- source completeness;
- uninterrupted uptime;
- high-availability or multi-region operation;
- production traffic or user scale;
- penetration-test or security certification;
- legal review or compliance certification;
- official provider partnership.

## Why this evidence matters

The strongest signal is not the raw test count. It is the correspondence between:

```text
product invariant
↕
automated test
↕
observed failure
↕
general remediation
↕
release gate
↕
verified production revision
```

That chain is what turns Civic from a described architecture into an engineered product.
