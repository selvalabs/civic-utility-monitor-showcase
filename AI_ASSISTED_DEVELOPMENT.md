# AI-assisted development

## What the term means here

Civic was developed with substantial AI assistance across analysis, implementation, testing, documentation, and audit preparation.

That does not mean the product was generated from one prompt, that every proposed change was accepted, or that an AI system was allowed to define production truth.

The engineering model was:

```text
human product intent and constraints
        ↓
AI-assisted analysis and implementation
        ↓
independent evidence gates
        ↓
review, CI, controlled deployment, production verification
```

## Where AI assistance contributed

### Repository and system mapping

AI assistance helped trace flows across source adapters, persistence, matching, notification creation, member access, delivery events, and deployment documentation.

This was useful for identifying cross-layer behavior that would be easy to miss when reading one module at a time.

### Research and design comparison

External-source behavior, framework conventions, security boundaries, and alternative designs were compared before changes were proposed. Research was treated as input to a decision, not authority to change the product.

### Implementation

AI-assisted coding was used for feature work, refactoring, tests, and documentation. Changes still had to fit existing models, migrations, contracts, and production constraints.

### Test construction

AI assistance helped turn product requirements into adversarial cases, integration paths, browser checks, and explicit readiness categories.

The tests were allowed to fail. Their purpose was to challenge the implementation, not confirm that generated code looked plausible.

### Audit and remediation

AI assistance was used to build evidence matrices, classify findings, review diffs, and design focused remediation tasks. Product, CI, and public-content problems were handled in separate changes rather than folded into one opaque rewrite.

## The control system

### Product invariants came before green tests

For matching, the core invariant was defined independently:

> insufficient or meaningfully different street information must not create an individual resident alert.

The implementation was then tested against that rule.

### Failures were preserved

The first readiness run returned a release-blocking false positive. The result was recorded rather than immediately editing the assertion or hiding the case inside a broader green count.

### Remediation was separated from discovery

The readiness task could add evidence but could not fix product behavior. A later focused branch changed the street-eligibility boundary. This preserved a clean record of what failed and why.

### Diffs were reviewed, not only outcomes

The correction was checked for:

- hardcoded examples;
- accidental municipality changes;
- unrelated refactors;
- weakened tests;
- mutation residue;
- changes outside the intended boundary.

### CI and security controls were independent

Formatting, typing, tests, dependency audit, secret scanning, hygiene, container checks, and integration contracts ran outside the conversational reasoning that proposed the change.

A GitHub capability that was unavailable for the private repository was classified truthfully as N/A instead of being presented as a passing security control.

### Deployment was a separate proof

A locally certified commit was not described as “in production” until the merged revision, built image, running services, health/readiness, smoke tests, and live-source checks were tied together.

## PRD-001 as an example

The matching defect illustrates the full control loop.

1. AI assistance helped create a cohesive product-readiness suite.
2. The suite found that a generic set-based similarity function could assign a perfect score to two materially different fictitious streets.
3. The result blocked the release.
4. A separate remediation changed the general street-eligibility policy.
5. Positive and negative regression matrices passed.
6. A disposable mutation proved the tests would detect the old unsafe behavior.
7. The candidate passed local and remote gates.
8. The exact merged revision was deployed and verified.

The useful outcome was not that AI wrote tests or code. It was that the process could reject an apparently confident result and force the implementation back under the product invariant.

## AI in development versus AI at runtime

These are deliberately different concerns.

### Development-time assistance

Used for:

- analysis;
- research;
- implementation support;
- test design;
- documentation;
- audit and remediation planning.

### Runtime product decisions

Not delegated to an LLM:

- source precision;
- resident eligibility;
- street identity;
- notification creation;
- source-specific message semantics;
- delivery success state.

Resident-facing matching and notification rules are deterministic and reviewable.

## Human responsibility

Human review remained responsible for:

- the product boundary;
- what counted as acceptable evidence;
- deployment authorization;
- disclosure and privacy decisions;
- whether a finding blocked promotion;
- what the public case study could safely claim.

Formal legal review remains outside the evidence represented by this showcase.

## Why document this

AI-assisted engineering is most credible when the controls are visible.

The relevant claim is not “AI built this.” It is:

> AI accelerated the work inside a process that could still detect errors, reject unsafe behavior, preserve evidence, and verify the deployed result.
