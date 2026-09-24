# Conformance and Governance

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](./Glossary.md)

> The standard succeeds when independent implementations behave compatibly under shared tests, not when they copy one reference product.

## At a Glance

- The project separates a readable concept layer from future normative specifications.
- Clients and Resources claim explicit Profiles and protocol versions, not vague “full compatibility.”
- Registries provide stable names for extensible concepts.
- Conformance includes failure and security tests, not only successful rendering.
- Governance records decisions, compatibility impact, and implementation evidence in public.

## Documentation Levels

### Concept

Explains the system to ordinary readers. It uses **Current Direction**, **Current Proposals**, and **Open Decisions**, not normative keyword-heavy prose.

### Architecture

Defines actors, data ownership, trust boundaries, lifecycles, and observable behavior. It remains implementation-neutral where possible.

### Technical Specification

Defines exact schemas, algorithms, interfaces, protocol messages, error handling, and test assertions. Only this level uses `MUST`, `SHOULD`, and related normative keywords deliberately.

The current vault is primarily Concept and Architecture.

## Conformance Units

One product may conform to several independent units:

- Client Profile;
- Resource Type and Manifest version;
- World behavior host API version;
- network host API and advertised transport Features;
- optional Session Profile and its Transport Bindings;
- World Service interface;
- Avatar or Item profile;
- Resource Relay privacy profile;
- Device Adapter profile;
- optional graphics profile.

This avoids an all-or-nothing “metaverse compatible” badge.

The platform does not require every World to use one Session Profile. However, a claimed profile has real requirements, including a common transport binding or explicit interoperability path. Custom application code is tested against its declared host interfaces, not automatically against an unrelated shared message schema.

## Registries

Stable registries are expected for:

- Client Profiles;
- Features and Limits;
- Input Signals;
- Permissions;
- Resource Types and Manifest fields;
- component and host interfaces;
- service roles and protocol versions;
- content labels;
- cryptographic algorithms and canonical encodings;
- extension namespaces.

Unknown optional entries are ignored or disabled safely. Unknown required entries cause a clear compatibility failure. Registration policy should prevent name squatting without requiring one vendor’s approval for experimentation.

## Test Strategy

The first conformance suite should verify that at least two independent Clients can:

1. resolve the same World Address;
2. fetch one Release from different Mirrors;
3. compute and verify the same Release ID;
4. load the same baseline World scene and produce acceptable output;
5. run the same behavior component through a versioned host API;
6. operate without an account or network service;
7. deny undeclared filesystem, network, sensor, and identity access;
8. use declared fallbacks for unsupported optional Features;
9. contain malformed content and a crashed World;
10. report comparable structured errors.

Manifest and variant-selection tests include a single portable path, shared files with several alternatives, missing files exclusive to an unselected variant, incompatible required formats, and invalid selected files. Selection happens before content decoding or behavior execution. Different compatible quality choices retain the same Release ID.

Clients also open a cached Release without server-assisted selection, reject an incompatible server suggestion, and reject altered bytes served under a declared hash. Test observations include which files were requested, so an unsupported heavy variant is not downloaded or decoded merely to discover incompatibility.

Later suites add sessions, Avatars, Resource Relays, identity, Items, Overlay Apps, devices, and graphics profiles. Avatar and Item tests include hostile behavior components that attempt cross-Resource mutation, undeclared host calls, prompt creation, and budget exhaustion.

Resource Relay tests cover both remote-host retrieval and direct participant upload of the same Release, cache hits by Release ID, admission-limit fallback, hash verification, and absence of mandatory direct viewer-to-origin connections.

Message Relay tests cover sender authentication, session and channel separation, message size and rate limits, delivery classes, replay rejection, backpressure, reconnect, and rejection of updates from the wrong authority role.

Network API tests run both a custom sandboxed protocol and a shared Session Profile through the same broker. They cover binary buffers, framing, unavailable transport Features, truthful fallback properties, cancellation, bounded queues, credential isolation, destination changes, and grant revocation. Selecting a custom protocol never permits a cross-principal connection or a silent direct-peer privacy downgrade.

Adaptive pose-profile tests vary forwarding rates and detail levels, lose and reorder updates, change rigs, and reconnect. Different Replication Policies should remain compatible. Tests verify explicit missing-data and recovery rules rather than a specific distance algorithm or fixed tick rate.

A sample World may demonstrate server-authoritative reconciliation; conformance checks safe delivery and scoped state application, not the correctness of one universal prediction algorithm.

Tests include:

- valid examples and canonical test vectors;
- invalid, truncated, oversized, and cyclic content;
- permission denial and revocation;
- Resource Sandbox handle isolation and typed World mediation;
- incompatible versions and missing fallbacks;
- replay, reconnect, and service failure;
- resource exhaustion and cancellation;
- parser, compiler, and protocol fuzz corpora;
- privacy assertions where behavior can be observed.

Visual conformance should focus on defined invariants and tolerances, not pixel-identical output across GPUs.

The later portable raster suite adds parameter-layout agreement across backends, restricted shader imports, bounded compilation and specialization, invalid target code, standard-material fallback, and rebuilding stale native caches. Runtime GPU behavior still needs platform-specific stress testing; passing static validation is not proof against driver failure.

## Versioning and Extensions

- Published Releases remain immutable.
- Shared protocol contracts identify versions through declarations or negotiation before exchanging state that depends on them; not every custom service needs an extra handshake.
- Additive optional fields are preferred when old implementations can ignore them safely.
- A required semantic change receives a new version or Profile.
- Experimental extensions use scoped namespaces and cannot redefine core behavior.
- Widely implemented extensions may enter the standard through evidence and review.
- Deprecated algorithms and interfaces include transition and failure rules.

## Governance Process

A decision proposal should state:

- problem and user need;
- affected actors and trust boundaries;
- alternatives and tradeoffs;
- applicable modern web standards and the reason for reuse, adaptation, or replacement;
- privacy and security impact;
- compatibility and migration impact;
- implementation experience;
- conformance plan;
- decision owner and status.

Major changes should require multiple independent implementation attempts before becoming a mandatory core contract. Governance should be vendor-neutral, transparent, and capable of urgent security action without allowing emergency powers to become ordinary product control.

## Security Operations

The ecosystem needs coordinated but non-centralized support for:

- vulnerability intake and responsible disclosure;
- identifiers for affected specifications and Releases;
- signed advisories and local block policy;
- publisher and account key rotation;
- dependency and compiler inventories;
- test corpus updates;
- time-bounded emergency mitigations;
- post-incident compatibility review.

A revoked Release remains immutable and may remain mirrored. Normal Clients can refuse to activate it, but the protocol cannot erase public copies.

## Current Proposals

- Ratify only a small Core 3D profile first.
- Require two independent implementations and a public conformance suite for core features.
- Maintain one central decision register in [Open Decisions and Roadmap](./Open%20Decisions%20and%20Roadmap.md).
- Track applicable web use cases and unresolved gaps in [Web Platform Alignment](./Web%20Platform%20Alignment.md), distinguishing long-term coverage from the current prototype.
- Use open registries with a lightweight review process and vendor-scoped experimentation.

Documentation licensing and project attribution are defined in [Authors and Attribution](../AUTHORS.md). That CC BY 4.0 publication decision does not establish a software license or a standards patent policy.

Exact governance membership, voting, standards intellectual-property policy, registry process, and certification marks remain open.

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Roadmap: [Open Decisions and Roadmap](./Open%20Decisions%20and%20Roadmap.md)
- Threat model: [Security and Privacy](../Trust/Security%20and%20Privacy.md)
- Terms: [Glossary](./Glossary.md)
