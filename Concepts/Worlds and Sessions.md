# Worlds and Sessions

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> A World is a portable interactive application. It may run by itself or connect to independent services for shared sessions and persistent state.

## At a Glance

- A World Release contains scene and component data, content files, behavior modules, compatibility requirements, and fallbacks.
- An [AR experience — XR and Passthrough](../Client%20Platform/Client%20and%20Runtime.md) is an ordinary World using supported presentation and spatial interfaces, not a separate Resource Type.
- [glTF](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html) can describe delivered 3D scenes, not just individual models, but it is not a complete World application format.
- Portable behavior needs a small, versioned host API and a sandbox; [WebAssembly](https://webassembly.github.io/spec/core/) alone does not define that API.
- Network transport and state authority are separate choices.
- Worlds may use custom application protocols through the safe [network host API](../Client%20Platform/Networking.md), or adopt optional shared Session Profiles.
- Online deployments select the service roles they need; a shared Session Profile describes participating relay and authority roles separately, not as a compulsory bundle.
- A relay can carry or order messages, but it does not make participant Clients honest.

## World Release

A World Release extends the common [Resource Model](./Resource%20Model.md) with:

- one or more scene entry points;
- scene and component data;
- content files and optional variants;
- sandboxed behavior components;
- required Client Profile, Features, Limits, and Permissions;
- service interfaces and supported protocol versions;
- standalone behavior and other fallbacks;
- declared resource and execution budgets.

### Why glTF is not the whole World format

glTF is designed for efficient delivery of runtime 3D scenes and models. It can carry geometry, materials, animation, cameras, lights, and extensions. A World also needs behavior lifecycle, Permissions, networking, authority, persistence, interaction semantics, and failure rules.

The current direction is therefore:

```text
World Manifest
├── scene entry points
├── scene and component data
├── content files such as glTF/GLB
├── behavior components
├── service interfaces
└── variants and fallbacks
```

The standard should reuse glTF rather than invent another mesh format, while defining the missing application-level contracts around it. It does not require visual meshes, collision data, and interaction state to be stored separately. A World may generate or update them at runtime through its behavior APIs and authority rules.

### Connecting the standards

The Manifest identifies the scene entry point, content formats, behavior modules, and required interface versions. A World component model gives application meaning to scene objects: stable references, component types, interaction hooks, and controlled runtime creation or mutation. [Graphics and Shaders](../Client%20Platform/Graphics%20and%20Shaders.md) defines how materials and shader parameters connect to the renderer.

These are separate responsibilities, not necessarily separate files or duplicated scene trees. The component binding may use references or an agreed scene extension; the exact encoding is still open. glTF alone does not define these application semantics.

### Current Proposals: glTF and OpenUSD

- Use a specified [glTF/GLB](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html) subset as the first delivered scene representation. glTF is designed for efficient runtime delivery of scenes and models. See the [Khronos overview](https://www.khronos.org/gltf/).
- Allow authoring tools to assemble scenes with [OpenUSD](https://openusd.org/release/intro.html) and export to that representation. OpenUSD offers layers, references, and variants; requiring its full composition system in every Client would expand implementation scope.
- Do not assume a USD scene can reference glTF content interoperably without an explicit binding and resolver contract. A runtime USD profile would be a separate future decision, not a prerequisite for opening Worlds.

These format choices still need prototype evidence. The agreed direction is to reuse scene formats and add the missing application contracts, not to standardize one editor.

## Portable Behavior

World behavior runs as isolated components with explicit imports and exports. It does not receive operating-system access simply because it was downloaded.

The leading candidate is the [WebAssembly Component Model](https://component-model.bytecodealliance.org/) with **[WIT](https://github.com/WebAssembly/component-model/blob/main/design/mvp/WIT.md)** interfaces because it can describe typed strings, records, resources, errors, and versioned host calls across programming languages. The standard still needs its own narrow interfaces for entities, time, input, audio, networking, storage, and user-approved device access.

General-purpose filesystem and network interfaces are not enabled automatically. A World talks through Client brokers that enforce its Application Principal, Permission Grants, destination policy, quotas, and lifecycle.

WebAssembly isolates module memory from the host; it does not make bugs inside a module impossible. Host APIs, parsers, and native engine code remain part of the security model.

## Nested Resource Behavior

Avatar and Item code runs in a Resource Sandbox inside the World Runtime. It may share the World scheduler and process, but it has separate memory, quotas, Application Principal, Permission Grants, and host handles.

The default host interface exposes only a **Self Handle**. It may allow the Resource to animate its own model, update its own materials and effects, keep bounded local state, or emit a typed interaction request. The handle is not blanket authority even over itself: the profile lists writable fields, while authoritative position, collision, ownership, and shared state remain World-controlled. The API does not expose a global `setPosition(entityId, ...)`-style function or a way to obtain another participant's mutable handle.

To affect anything outside itself, Resource Behavior sends an intent through a World-defined integration interface. The World may reject it, translate it into a local cosmetic effect, or forward it to the authority owner. A platform Permission can expose sensitive local data or an external service; it never grants authority over another participant or shared World state.

The same Resource may therefore have different execution roles:

- **presentation behavior** runs on viewing Clients and changes only local presentation;
- **interaction behavior** submits validated requests to the World;
- **authoritative behavior** runs only where the World or service assigns authority.

The Release declares each behavior module, role, host API version, budgets, and possible Permissions. Unsupported or failed behavior falls back to the Resource's declarative presentation.

## Running a World

1. The Client validates the Release and selects compatible content and fallbacks.
2. The Client creates a World runtime with resource budgets and no automatic privileged access.
3. Behavior components receive only declared host interfaces.
4. The Client loads the selected scene, content, and components.
5. The World may run Standalone, resolve a shared Session Descriptor, or bootstrap its declared custom protocol and services.
6. If a Permission or service is unavailable, the World uses its declared fallback or refuses to start cleanly.

## Session Architecture

See [Architecture Overview](../Architecture%20Overview.md) for the full local/provider/other-Client map. This section owns session responsibilities and authority, not the internal deployment layout of a provider.

One World can have several sessions, and a Client may participate in several Worlds or sessions when supported. Opening a World, selecting a session, and switching local focus are different actions. [Social Join Requests — Join Requests and Invitations](./Identity%20and%20Profiles.md) address a person; their reply can invite the requester to a specific session. The destination still decides admission, and a participant cannot grant entry merely by sending a link.

An online session needs more than a label such as “relayed” or “authoritative.” Standard Session Profiles use a Session Descriptor to describe their shared contract. Custom networking libraries may define their own application messages and join flow while preserving platform security and declared service requirements; the standard does not require the Client Core to understand every custom message.

A shared Session Descriptor eventually needs to define:

- session and protocol versions;
- selected Session Profile, supported Transport Bindings, and explicit fallback choices;
- service endpoints and their authenticated operator;
- required service roles and which provider performs each role;
- join and authorization flow;
- participant and channel identifiers;
- initial snapshot and clock information;
- message envelopes, ordering, replay protection, and limits;
- reconnect and migration behavior;
- authority owner for each state domain;
- failure and fallback behavior.

### Session service roles

| Role | Responsibility |
| --- | --- |
| **Session Coordinator** | Creates or discovers session instances and handles the join flow |
| **[Message Relay](./Message%20Relay.md)** | Forwards realtime poses, events, and World messages between participants |
| **[Resource Relay](./Resource%20Relay.md)** | Fetches or accepts participant Avatars and Items, optionally caches them, and serves them indirectly |
| **Media Relay** | Routes latency-sensitive voice or other realtime media |
| **Authoritative World Server** | Validates and owns selected shared state domains |

A **Session Service Provider** may perform one or several roles. The Session Descriptor names each role separately so a deployment can use one integrated provider, several specialized providers, or self-hosted services.

These choices belong to a World deployment or session instance, not permanently to the immutable World Release. Different sessions of the same World may use different providers or regions. A Standalone World uses none of these remote roles.

[Message Relay](./Message%20Relay.md) and [Resource Relay](./Resource%20Relay.md) have separate service contracts, even when one operator or transport stack implements both. The Message Relay carries bounded realtime messages; it is not a general file host. The Resource Relay carries hash-identified immutable content and applies storage, upload, and cache limits. Their roles do not prescribe one mandatory transport for all Worlds.

### Session Regions and Placement

A deployment may offer several hosting regions, only one fixed server, or no region information. Multi-region hosting and a regional-selection UI are not requirements. The standard makes supported choices describable; it does not dictate how a provider allocates servers.

When selecting or creating a session, the Client can supply an optional region preference or request automatic selection. An authorised discovery or join flow can describe:

- whether regional choice and session creation are available at all;
- eligible sessions or placement options, with available region and service-role information;
- relevant admission, capacity, compatibility, and availability conditions;
- the actual selected session and approved connection endpoints.

These are conceptual information needs, not a new mandatory handshake or fixed schema. Private sessions are disclosed only to eligible callers. Provider estimates can become stale; admission and availability are checked again when joining.

An Invitation or session-specific address already selects a session. A Client may choose an approved endpoint for that same session, but a nearer session of the same World is not an equivalent substitute. Creating or joining a different session requires a new choice. An unavailable preferred region does not require a single-server host to create another deployment.

A soft preference can fall back to an available region under Client policy, with the result made clear. A strict regional restriction cannot silently fall back; an unsatisfied restriction produces a clear failure. Neither policy requires disclosing precise user location.

Service roles may be distributed: the simulation server, Message Relay, Media Relay, and Resource Relay need not all be in one place. An advertised regional label does not prove the location of every service or data copy. [Download-source preferences — Download Sources and Regional Preferences](./Publishing%20and%20Delivery.md) are independent of session placement.

### Transport topology

| Topology | Meaning | Main tradeoff |
| --- | --- | --- |
| **Standalone** | No shared network session | Private and simple, but no remote participants |
| **Peer-to-peer** | Participant Clients exchange some data directly | Low infrastructure cost, but exposes network metadata and weakens abuse control |
| **Message relay** | A service forwards messages between Clients | Hides peers from each other and simplifies routing, but adds service cost and trust |
| **Server mediated** | A World Server receives and processes session traffic | Strong control and persistence, but higher operating cost |

The privacy-oriented default avoids direct peer connections unless the Client's explicit user or managed privacy policy permits IP exposure. A deployment cannot silently opt the user into direct networking by choosing a different application protocol.

### State authority

Authority is assigned per state domain, not once for the entire World.

| Example domain | Possible authority | Why |
| --- | --- | --- |
| Head and hand pose | Participant, rate-limited by the service | High-rate ephemeral input |
| Shared object transform | Designated participant or World Server | Prevents conflicting writers |
| Competitive movement | Authoritative World Server | Limits cheating |
| Inventory and purchases | Authoritative World Server or issuer | State has lasting value |
| Voice media | Media service with local user controls | Different latency and privacy needs |
| Decorative local effects | Each Client | No shared consequence |

A Message Relay may sequence events, but identical ordering does not guarantee identical simulation. Deterministic simulation also requires specified clocks, random inputs, floating-point behavior, physics, and host APIs. It should be a separate strict profile, not an assumption of the base protocol.

Prediction, reconciliation, rollback, and fixed-step simulation remain World implementation choices. [Networking — Prediction and Reconciliation](../Client%20Platform/Networking.md) describes the platform interfaces that permit corrections without making them universal packet commands. [Message Relay — Replaceable Replication Policy](./Message%20Relay.md) describes variable-rate delivery without prescribing a global tick or provider algorithm.

## World Services

A creator may deploy a World in several ways:

- upload a Release and use third-party session services for coordination, messages, resources, and media;
- host a custom Authoritative World Server while using public content hosts;
- self-host both content and services;
- run entirely Standalone.

Services advertise versioned interfaces. Shared Session Profiles let providers interoperate without one vendor SDK; developers may also ship a custom sandboxed networking library and backend. The latter claims compatibility with its declared interfaces, not automatically with every shared relay profile. Sensitive tokens are limited to one service, session, action, and short lifetime where practical.

## Failures

- Failure of an optional service disables only the related feature.
- Failure of required authority ends or pauses the affected session safely.
- Reconnect never silently replays non-idempotent purchases or actions.
- A malformed message is contained to the responsible channel or participant.
- A World crash does not crash the Client or other Worlds.
- A Client that cannot satisfy declared session or network requirements explains why before joining.

## Current Proposals

- Use [glTF/GLB](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html) for visual assets.
- Evaluate [WebAssembly Component Model](https://github.com/WebAssembly/component-model/blob/main/design/mvp/Explainer.md) and [WIT](https://github.com/WebAssembly/component-model/blob/main/design/mvp/WIT.md) for behavior ABI.
- Define transport-independent messages for optional shared Session Profiles, then specify interoperable bindings without imposing them on custom application protocols.
- Describe authority in a state-domain table inside the Session Descriptor.
- Require a Standalone path for the first conformance World, even if later Worlds may require services.

The address scheme, World component schema, host API, session bootstrap, message format, physics scope, and authority model are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Packaging: [Resource Model](./Resource%20Model.md)
- Runtime: [Client and Runtime](../Client%20Platform/Client%20and%20Runtime.md)
- Compatibility: [Features and Permissions](../Client%20Platform/Features%20and%20Permissions.md)
- Network API: [Networking](../Client%20Platform/Networking.md)
- Graphics: [Graphics and Shaders](../Client%20Platform/Graphics%20and%20Shaders.md)
- Realtime messages: [Message Relay](./Message%20Relay.md)
- Participant resources: [Resource Relay](./Resource%20Relay.md)
