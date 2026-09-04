# Product

## Product statement

Civic Utility Monitor is an independent system that monitors supported public electricity and water-service interruption notices and prepares possible-impact alerts for people who voluntarily register an address.

The current deployed scope is Santa Catarina, Brazil, with public-source support for:

- Celesc scheduled outage notices;
- Celesc emergency outage information;
- CASAN water-service notices.

Civic is not operated, sponsored, or maintained by those providers.

## Why it exists

A public notice can exist without reaching every person who needs it. The utility account may belong to a landlord, relative, business owner, or another household member. Contact information may be outdated, and notices are distributed across different public pages and feeds.

Civic reduces that information gap without requiring credentials for a private consumer account.

## Intended users

### Residents

People who want to monitor the address where they live, even when they are not the official account holder.

### Small local operations

Teams or small businesses that need advance awareness of possible interruption at a registered address.

### Operators

People responsible for maintaining source compatibility, reviewing health signals, and managing failures through controlled product surfaces.

## Resident journey

```text
register address and contact
→ accept current terms
→ verify email
→ confirm phone through the delivery boundary
→ establish member access
→ choose alert sources
→ review alerts and history
→ update address or preferences
→ delete product data
```

The public product supports Brazilian Portuguese, English, and Spanish.

## What an alert means

An alert means:

> A supported public notice contains enough location information to indicate that the registered street may be affected.

It does not mean:

- the provider confirmed the resident's private consumer unit;
- service will definitely be interrupted;
- the notice is complete or current;
- the external message provider guaranteed delivery.

The wording deliberately uses possible-impact semantics and directs residents back to official provider channels for confirmation and service.

## Location policy

Municipality is required as context. Neighborhood may be preserved as source context. Neither is sufficient to create an individual resident alert.

Street identity is the final eligibility boundary. Representation differences can normalize, for example:

```text
Rua das Flores
R. das Flôres
```

Meaningful differences fail closed:

```text
Rua das Flores
Rua das Flores do Sul
```

When the public notice does not publish a street, Civic may retain the notice for source history and diagnostics, but it does not promote that record into an individual alert.

## Supported product flow

1. **Collect public notices.** Each supported source has an isolated adapter.
2. **Preserve uncertainty.** Missing fields remain missing.
3. **Normalize the domain record.** Source-shaped data becomes a common notice lifecycle.
4. **Filter by resident state and preferences.** Only active, approved residents and enabled sources are considered.
5. **Apply conservative street matching.** Ambiguous identity does not qualify.
6. **Persist the result.** Matching, notification, and delivery state are auditable.
7. **Cross a narrow delivery boundary.** Transport can retry or fail without redefining product policy.
8. **Keep resident control.** Members can review state, update preferences and address, log out, and delete their product data.

## Synthetic alert example

The following is illustrative and contains no real resident or provider record:

> **Scheduled electricity notice may affect your registered street**  
> Hello, Alex. A supported public notice includes the street registered with Civic.  
>  
> Registered address: Rua das Flores 123 — Centro  
> Published schedule: 4 September, 14:00  
>  
> Confirm current information through the provider's official channels.

The production product keeps source-specific meaning and uses deterministic templates rather than free-form generated copy.

## Product principles

### Preserve the source boundary

Public information is untrusted and may change. Parsing success does not make a notice complete or authoritative.

### Prefer missed automation to misleading precision

A false positive can cause unnecessary preparation and reduce trust. Uncertain location data therefore fails closed for individual delivery.

### Persist before transport

A notification and its delivery state exist as product records before an external transport attempts delivery.

### Let residents control their data

The product minimizes collected fields, records consent context, and includes authenticated self-service deletion.

### Make non-claims explicit

Engineering quality includes saying what the product does not prove.

## Current limitations

- Coverage is limited to selected public sources in Santa Catarina.
- Public pages and feeds can change without notice.
- Matching is textual rather than geospatial.
- A valid notice may be missed when a provider publishes a materially different street form.
- Source disappearance is not automatically treated as restoration.
- Source text is not machine-translated.
- External delivery can fail or be delayed.
- The showcase does not publish production uptime, traffic, user-count, or delivery-rate metrics.
- Formal legal review is not represented as completed.

## Live product

The public product is available at:

[monitorcomunitario.soberania.cloud](https://monitorcomunitario.soberania.cloud)

Availability is presented as a current demonstration, not an SLA.
