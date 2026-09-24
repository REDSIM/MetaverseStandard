# Security and Privacy

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> The Client treats downloaded content and remote services as untrusted, grants only explicit authority, and limits how activity can be linked across Worlds.

## At a Glance

- The user trusts the chosen operating system and Client Core; a World never receives that trust automatically.
- Shared Worlds should not trust participant Clients to enforce valuable state.
- Every active application has an Application Principal that owns its storage, Permission Grants, network policy, and audit trail.
- Avatar and Item code runs in nested Resource Sandboxes with self-scoped authority by default.
- Signatures establish origin and hashes establish integrity; neither establishes safety.
- Privacy mechanisms reduce exposure and correlation. They do not promise complete anonymity.

## Two Trust Perspectives

### Protecting the user

The local Client Core and operating system are trusted to isolate Resources, display authentic System UI, protect credentials, and enforce grants. Worlds, content files, behavior code, device adapters, and remote services are untrusted inputs.

### Protecting shared state

World operators cannot assume that participant Clients run an honest implementation. A Client can lie about local simulation, timing, movement, or inventory. Valuable shared state needs an explicitly authenticated authority, normally an Authoritative World Server or issuer.

An authoritative server can enforce its rules; it does not prove that its operator respects users or handles data honestly.

## Security Invariants

| Invariant | Canonical owner |
| --- | --- |
| Downloaded behavior starts without device, file, identity, storage, or network authority | [Client and Runtime](../Client%20Platform/Client%20and%20Runtime.md) |
| The Manifest declaration is not a Permission Grant | [Features and Permissions](../Client%20Platform/Features%20and%20Permissions.md) |
| Bootstrap operations do not give unverified World code network or account access | [Platform Services](../Concepts/Platform%20Services.md) |
| Grants do not transfer through embedding, equipping, or dependencies | [Features and Permissions](../Client%20Platform/Features%20and%20Permissions.md) |
| Automatically loaded participant code has only self-scoped handles, strict budgets, and no permission prompts | [Resource Model](../Concepts/Resource%20Model.md) |
| Releases are immutable, hash-bound, and have pinned dependencies | [Resource Model](../Concepts/Resource%20Model.md) |
| Mirrors cannot change verified content | [Publishing and Delivery](../Concepts/Publishing%20and%20Delivery.md) |
| Third-party participant content is not fetched directly by default | [Publishing and Delivery](../Concepts/Publishing%20and%20Delivery.md) |
| Untrusted parsing, decoding, and compilation are isolated | [Publishing and Delivery](../Concepts/Publishing%20and%20Delivery.md) and [Client and Runtime](../Client%20Platform/Client%20and%20Runtime.md) |
| One World cannot crash or inspect another World | [Client and Runtime](../Client%20Platform/Client%20and%20Runtime.md) |
| Pairing a device does not grant a World access to its data | [Devices and Input](../Client%20Platform/Devices%20and%20Input.md) |
| Passthrough composition does not imply camera access | [Client and Runtime](../Client%20Platform/Client%20and%20Runtime.md) |
| A Message Relay does not prove simulation correctness | [Worlds and Sessions](../Concepts/Worlds%20and%20Sessions.md) |
| Custom application protocols cannot bypass network or state-authority policy | [Networking](../Client%20Platform/Networking.md) |
| Trusted emergency controls remain locally available | [Safety and Moderation](./Safety%20and%20Moderation.md) |

This page owns the threat model; linked domain pages own the detailed behavior so the same rule is not rewritten in several places.

## Application Principal

Every World, installed Overlay App, and independently active Resource needs a security identity comparable to a [web origin](https://html.spec.whatwg.org/multipage/browsers.html#origins). The Application Principal binds:

- persistent storage and caches;
- Permission Grants and revocation;
- allowed service destinations and tokens;
- identity disclosures;
- inter-application communication;
- update inheritance;
- logs, reputation, and local policy.

The principal should combine a stable Resource ID, verified publisher authority, and a security epoch. A normal update can retain appropriate grants; publisher recovery or a security reset can deliberately break inheritance.

Cross-principal communication is denied unless a typed interface and both relevant policies allow it.

## Main Threats and Defenses

| Threat | Primary defense |
| --- | --- |
| Malicious World code | Memory sandbox, narrow host interfaces, brokered access, quotas |
| Parser or media exploit | Isolated validation workers, strict limits, patched implementations |
| Fake permission or account UI | Protected System UI, secure-attention path, persistent indicators |
| Supply-chain replacement | Signed Publication Records, immutable hashes, pinned dependencies |
| Host tracking Avatar viewers | Resource Relays, shared caches, network-routing policy |
| Cross-World tracking | Guest and pairwise identity, data minimization, separated storage |
| Stolen publisher or account key | Key separation, rotation, recovery, security epochs, audit history |
| Cheating participant Client | Authority assigned to a World Server for valuable state |
| Abusive service endpoint | Authenticated operator, scoped tokens, destination policy, rate limits |
| Denial of service | Budgets, timeouts, cancellation, isolation, retry and fallback |

## Network Security

Transport freedom creates risks of unauthorized destination access, cross-principal credential leaks, network-address exposure, and resource exhaustion. The [Network Broker — Network Broker and Trust](../Client%20Platform/Networking.md) owns the detailed controls for all application protocols, including public-service authentication and encryption, destination checks, scoped credentials, and quotas.

Message protocols still need replay handling and application-level authorization. An authenticated connection does not make every received command valid. Direct peers and approved service operators can observe connection metadata; changing transports does not remove that privacy boundary.

## Privacy Model

The Client minimizes data at four points:

1. **Identity** — disclose a guest, pairwise, or selected Profile rather than a universal account by default.
2. **Sensors** — separate camera, spatial data, tracking, audio, and composition; reduce precision when possible.
3. **Destinations** — distinguish local processing from sending data to a named operator.
4. **Context** — keep Worlds, Profiles, Overlay Apps, and storage partitions separate.

Exact hardware and feature reporting is bucketed to reduce fingerprinting. Background capture has visible indication. Bystander-sensitive sensors need particularly clear purpose and retention notices.

A **Data Use Notice** describes what a remote operator claims it will do. It is useful for consent and accountability but cannot technically prove deletion or honest behavior.

“Local processing” is an enforceable boundary only when data outputs remain controlled. Giving application code both raw sensor data and arbitrary network access defeats that guarantee. [Capture and export rules — Local Processing and Network Export](../Client%20Platform/Features%20and%20Permissions.md) distinguish mediated operations from this broader access.

## Residual Risks

The standard cannot fully protect against:

- a compromised operating system or chosen Client;
- screenshots, external recording, or data copied after legitimate decryption;
- traffic analysis, timing, file sizes, and colluding relays;
- GPU driver and other shared native-kernel failures;
- correlation through voice, motion, Avatar, social graph, or behavior;
- an authorized remote operator misusing data;
- physical observation of the user or device;
- legal compulsion or provider shutdown.

These limits should be stated directly rather than hidden behind the word “decentralized.”

The goal is layered, testable protection, not a promise of complete safety. Permission prompts, firewalls, signatures, and sandboxing each address different threats; none replaces the others.

## Security Assurance

Security depends on more than document rules. A mature standard needs negative conformance tests, threat-model updates, fuzz corpora, dependency and compiler tracking, vulnerability reporting, security advisories, release revocation, and interoperable recovery procedures. See [Conformance and Governance](../Reference/Conformance%20and%20Governance.md).

## Current Proposals

- Make the Application Principal an early core decision.
- Keep the Client Core small and move risky native processing into restartable workers.
- Require nested self-scoped sandboxes for automatically activated Avatar and Item code.
- Define privacy levels for Resource and session relays instead of promising anonymity.
- Treat direct peer networking as opt-in where viewer IP privacy matters.

Principal construction, key recovery, relay privacy levels, network broker policy, and security response are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Immediate protection: [Safety and Moderation](./Safety%20and%20Moderation.md)
- Permissions: [Features and Permissions](../Client%20Platform/Features%20and%20Permissions.md)
- Publication: [Publishing and Delivery](../Concepts/Publishing%20and%20Delivery.md)
