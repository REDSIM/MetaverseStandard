# Resource Relay

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> A Resource Relay indirectly delivers participant Resources from a host or owner Client and may cache them for a session.

## At a Glance

- Viewers do not connect directly to a participant's device or personal Resource Host.
- Hosted and locally stored Resources use the same immutable [Release](./Resource%20Model.md) format.
- The relay may fetch a Release from a Resource Host or accept it from the owner's Client.
- Hash-based caching avoids repeated uploads and downloads.
- Admission limits reject only the affected Resource when a fallback is possible.
- The relay verifies signed publication metadata and content integrity, not safety, authorship, or permission to publish.

## Source Paths

```text
Resource Host ───────┐
                     ├──→ Resource Relay ───→ Viewer Clients
Owner Client upload ─┘
```

A participant announces a Release ID and optional host locations when joining a session. If the relay already has every required Content File, no upload is needed. Otherwise it fetches missing files from a Resource Host or accepts them from the participant's Client.

A participant-hosted Avatar or Item may exist only on its owner's device before the session. The relay then acts as a temporary Mirror while the participant is present. Permanent hosting is optional.

For Releases with [Resource Variants — One Release, Optional Variants](./Resource%20Model.md), availability is tracked by Content File and complete content path, not just by the presence of a Release ID. A relay need not cache every alternative. It exposes which declared paths it can serve, accepts or fetches missing files within its policy, and lets each viewer select compatible content. Shared files are stored once; unavailable paths use declared fallbacks or a placeholder, never a silent direct-origin fetch.

## Delivery Flow

1. The Session Descriptor identifies the Resource Relay and its policy.
2. The participant announces a Release ID and available source paths.
3. The relay checks its content-addressed cache.
4. The relay authenticates the participant and performs Resource Admission for missing files.
5. The owner Client uploads them, or the relay fetches them from a Resource Host.
6. The relay verifies the Manifest, publication signature, Release ID, and file hashes before advertising availability.
7. Viewers fetch from the relay or reuse a verified local Client Cache entry.
8. Every viewer independently validates the Release before activation.

The local Client Cache is not automatically a redistribution service. A Client uploads a locally held Resource only when it is acting as an authorized source for that Resource.

For restricted delivery, a cache hit does not remove recipient or session access checks. Shared bytes may be deduplicated without making restricted files publicly retrievable. [Usage proofs](./Commerce%20and%20Usage%20Rights.md) stay separate from the shared content package: receiving an Avatar to render it does not grant the viewer the right to wear or resell it. This does not make the relay a compulsory copyright or purchase verifier.

## Resource Admission

Before transfer, the relay advertises:

- maximum Release, file, and expanded sizes;
- dependency and file-count limits;
- supported Resource Types and formats;
- upload bandwidth, timeout, and rate limits;
- session authorization and per-participant quota;
- cache duration and storage policy.

If an Avatar or Item exceeds a limit, the Client explains the failure and uses its declared fallback. The participant should still be able to join unless the session explicitly publishes a stricter rule.

## Cache Lifecycle

Content hashes allow one cached copy to serve every reference to the same immutable file. Cache retention is a provider policy: files may expire when the participant leaves, remain for a stated time, or be retained while frequently used.

The Resource Relay is not the Publisher and cannot change a verified Release. Long-term availability still comes from Resource Hosts, Mirrors, or a participant retaining a local copy.

## Failure Behavior

- A cache hit remains usable if its Release still passes current Client policy.
- Interrupted uploads resume or retry by immutable Content File hash rather than duplicating completed data.
- If required content is unavailable, viewers use the Resource's placeholder or fallback.
- Relay failure does not silently fall back to a direct viewer-to-origin connection. Direct retrieval requires an explicit session policy and user-visible privacy choice.
- Failure of one participant Resource does not terminate the World Runtime or unrelated transfers.

## Privacy and Trust

The relay avoids a direct viewer-to-origin connection: the origin sees the relay's connection instead of the viewer's. An owner uploading through the relay need not advertise a directly reachable address. This does not make all addresses secret: a published host URL may reveal its origin, and the relay normally sees connection addresses, participant, session, Resource, timing, and transfer metadata. A stronger optional privacy profile may separate relay and gateway roles.

The same Session Service Provider may operate Resource Relay, [Message Relay](./Message%20Relay.md), and Media Relay roles. Separate service contracts allow deployments to choose specialized or self-hosted services and make the combined privacy exposure visible. [Transport freedom](../Client%20Platform/Networking.md) does not permit custom World code to silently bypass participant-content routing.

## Related Topics

- Publication model: [Publishing and Delivery](./Publishing%20and%20Delivery.md)
- Session services: [Worlds and Sessions](./Worlds%20and%20Sessions.md)
- Resource format: [Resource Model](./Resource%20Model.md)
- Threat model: [Security and Privacy](../Trust/Security%20and%20Privacy.md)
- Terms: [Glossary](../Reference/Glossary.md)
