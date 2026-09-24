# Open Metaverse Standard

[Home](./README.md) · [Architecture](./Architecture%20Overview.md) · [Glossary](./Reference/Glossary.md)

> A proposal for an open platform where people enter interactive Worlds through compatible Clients, much as they open websites through compatible browsers.

## What This Is

The project is not one virtual universe, one application, or one company-owned service. It is a shared set of rules that could let independently built Clients, Worlds, services, and portable content work together.

The goal is broad interoperability without giving downloaded content unrestricted access to the user's device. A World should be easy to open, but it should run with the isolation and permission controls expected from a modern application platform.

This vault currently describes a **concept architecture**, not a finished specification. Stable directions, implementation candidates, and unanswered decisions are identified separately.

## The Idea in One Minute

| Concept | Plain explanation |
| --- | --- |
| **Client** | The user-chosen trusted application that opens Worlds and protects the local device. It plays a role similar to a browser, game engine, and XR shell. |
| **World** | A portable interactive application or space. It may run alone or connect to online session services. |
| **Profile** | A chosen identity presentation: name, picture, and selected information, with visibility controls. It can be local or linked to a federated account. Avatar choice is separate and may differ by World. |
| **Avatar** | Portable visual representation of a participant. |
| **Item** | Portable content integrated into a World, including fixed signs and dynamically loaded panels. Pickup, movement, and inventory membership are optional. |
| **Overlay App** | A user-installed application that can appear across Worlds, such as a media panel or accessibility tool. |
| **Release** | One immutable published version of a World, Avatar, Item, or Overlay App. |
| **Collection** | A personal list of saved Worlds, Avatars, Items, or other Resources, shared only with the chosen audience. |
| **World Service** | An optional remote service for sessions, shared state, persistence, voice, or other online behavior. |

The website/browser analogy is useful, but incomplete. A World can contain real-time 3D content, executable behavior, multiplayer state, sensors, and user-generated objects. That makes isolation, permissions, performance limits, and graceful fallbacks central to the design.

An [AR World: XR and Passthrough](./Client%20Platform/Client%20and%20Runtime.md) can place virtual content in the physical surroundings using approved Client interfaces. Its interaction, persistence, and multiplayer logic remain ordinary World behavior. AR does not introduce another Resource Type or automatic camera access.

Worlds may dynamically load Items or generate scene objects from external data. Discovery, feeds, placement, and composition remain developer choices, not a required universal service. An independent user-controlled tool can instead be an Overlay App.

Worlds receive a permission-filtered, changing view of available input and device functions. Missing AR hardware or a controller does not impose a platform-wide download ban. Safe content preparation, entering a device-dependent mode, and granting access remain separate decisions. Physical peripherals use [device interfaces](./Client%20Platform/Devices%20and%20Input.md), while external programs connect through the optional [External App Bridge](./Client%20Platform/Software%20Integration.md).

Like a web application, a World may choose its own backend and application protocol. The Client supplies safe [networking interfaces](./Client%20Platform/Networking.md). Optional shared Session Profiles make common online functions easier to reuse. No single realtime transport or reconciliation algorithm is imposed on all Worlds.

Opening content, checking its Manifest, and using supported account, directory, or friend services are different: [shared platform protocols](./Concepts/Platform%20Services.md) let Clients do these without first running World-specific code.

## How the Pieces Fit Together

**[Architecture Overview](./Architecture%20Overview.md) contains the system map:** an SVG showing the local Client, independent provider roles, other participants, and the connections between them. Start there for the complete component inventory and simple deployment examples.

- **Resources are delivered content:** Worlds, Avatars, Items, and Overlay Apps share one publication model.
- **The Client runs isolated copies:** World Runtimes contain self-scoped Avatar and Item behavior. Overlay Apps have independent application contexts.
- **Services perform separate jobs:** content hosting, accounts, session coordination, message/resource/media relay, and authoritative state do not require one operator.

There is no single mandatory “orchestrator.” Local lifecycle, session admission, replication policy, and simulation authority are separate responsibilities. Logical separation does not require a separate process or server for every role.

No single ecosystem-wide provider is required. Accounts, content, directories, relays, and World Services may be operated by different organizations. Cryptographic identifiers and signatures establish which Release was published. They do not prove that its content is safe or trustworthy.

## When the Client Starts

The Client provides navigation independently of any home World: open a direct address, search a chosen World Directory, return to saved Collections, or select a Profile. A flat home/search panel is a practical recommendation, not a mandated UI. Optional 3D home and search Worlds remain ordinary sandboxed content.

No global directory or domain per World is required. [Finding Worlds](./Concepts/Finding%20Worlds.md) explains addresses, replaceable search, and indexing. [Collections and Sharing](./Concepts/Collections%20and%20Sharing.md) explains saved content and who can see it.

## What Happens When a User Opens a World

1. The user or another application gives the Client a **World Address**.
2. The Client resolves that address to a signed publication and an immutable Release.
3. The Client reads the verified Manifest and checks decoding and execution [Features, Limits, and Client Profiles](./Client%20Platform/Features%20and%20Permissions.md) before preparing heavy content. Live device availability is checked separately when a mode needs it.
4. It selects compatible content and optional [Resource Variants](./Concepts/Resource%20Model.md), then downloads only the selected files and dependencies and verifies their hashes.
5. The Client validates untrusted files and starts World behavior inside an isolated runtime.
6. The Client checks declared [Permissions](./Client%20Platform/Features%20and%20Permissions.md) when needed, reusing valid grants and asking only when a new user decision is required.
7. If the World is online, the Client joins its [session services](./Concepts/Worlds%20and%20Sessions.md). Otherwise, the World may run locally.
8. Unsupported optional features use declared fallbacks. A broken World is stopped without taking down the Client or other open Worlds.

One Release may work across devices or offer optional variants. Separate desktop and mobile builds are not required. The Client can select content without a server handshake. A service may assist but cannot override local compatibility and safety checks.

## Trust Model

There are two different viewpoints:

- **The user trusts the chosen Client and operating system** to protect local data, devices, identity, and system controls.
- **A shared World does not trust participant Clients** to enforce valuable multiplayer state. Competitive rules, purchases, and authoritative inventory require a server or another explicitly defined authority.

Downloaded Worlds, Avatars, Items, Overlay Apps, media files, device adapters, and remote services are untrusted by default. Avatars and Items may contain code, but automatically activated code runs only inside a nested Resource Sandbox. It starts with access to its own runtime instance, not to the World, other participants, the network, sensors, or persistent data.

See [Security and Privacy](./Trust/Security%20and%20Privacy.md) for the full boundary model.

## Core Principles

1. **Open implementation.** Anyone can build a Client or host compatible content and services.
2. **No mandatory central account or real-world verification.** Guest use and local Profiles remain possible. Federated accounts add portability and synchronization. Identity and age checks are external service choices, not protocol prerequisites. Individual Worlds retain their own admission policies. See [Identity and Profiles: Identity and Age Checks Are External Policies](./Concepts/Identity%20and%20Profiles.md).
3. **Secure by default.** Downloaded code starts with no ambient device, file, identity, or network access.
4. **Portable core, optional depth.** A small baseline works widely. Richer Client Profiles add XR, advanced graphics, devices, and other features.
5. **Predictable fallback.** Missing optional features reduce quality or functionality instead of causing a crash.
6. **User control and privacy.** The Client mediates sensitive data and avoids unnecessary cross-World identity correlation.
7. **Verifiable interoperability.** Independent implementations are judged by shared tests, not by matching one reference Client.
8. **Modern web first.** Reuse applicable web standards and preserve their useful application capabilities. Adapt or replace mechanisms only with a stated reason. See [Web Platform Alignment](./Reference/Web%20Platform%20Alignment.md).

## Core Versus Extensions

The first interoperable core should prove that two independent Clients can safely open the same World. It should cover addressing, immutable Releases, validation, a baseline 3D scene, sandboxed behavior, feature negotiation, permissions, local lifecycle, and conformance tests.

The following are important but should grow as separate profiles after the core is proven:

- federated accounts and social graphs.
- advanced Avatar rigs.
- advanced cross-World Item integrations and commerce.
- persistent Overlay Apps.
- custom shaders, compute, and ray tracing.
- arbitrary external devices.
- federated moderation.
- Client-as-operating-environment deployment.

This split limits version 1. It does not remove the larger vision.

The broader target includes applicable modern web use cases, not only multiplayer 3D scenes. Storage, media, navigation, accessible interfaces, background work, and web-content integration remain visible in [Web Platform Alignment](./Reference/Web%20Platform%20Alignment.md) even where detailed contracts are not yet written.

## Current Technology Direction

These technologies are candidates, not final commitments:

- **[glTF/GLB](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html)** for delivered 3D scenes and models, with separate application-level contracts. [OpenUSD](https://openusd.org/release/intro.html) may be an authoring input rather than a required runtime dependency.
- **[WebAssembly Component Model](https://github.com/WebAssembly/component-model/blob/main/design/mvp/Explainer.md) and [WIT](https://github.com/WebAssembly/component-model/blob/main/design/mvp/WIT.md)** for portable behavior and typed host interfaces.
- **browser-like brokered networking** with explicit transport Features. [HTTPS](https://www.rfc-editor.org/rfc/rfc9110.html#section-4.2.2), [WebSocket](https://websockets.spec.whatwg.org/), and [WebTransport](https://www.w3.org/TR/webtransport/) are candidates for different uses, not one universal mandatory transport.
- **content hashes and signed update records** for immutable Releases and mirrors.
- **readable [JSON](https://www.rfc-editor.org/rfc/rfc8259.html) Manifests**, with a constrained canonical representation for hashing and signing.
- **[WebGPU](https://www.w3.org/TR/webgpu/)-style features and limits** as a model for portable graphics negotiation.
- **[VRM 1.0](https://vrm.dev/en/vrm1/)** as input to a Humanoid Avatar profile.
- **a versioned portable shader profile with Client-side compilation** for custom graphics. [Slang](https://shader-slang.org/slang/user-guide/) is an authoring and cross-compilation candidate, not an initial mandatory format.
- **[AT Protocol](https://atproto.com/specs/atp) concepts** as input to federated identity design, not a protocol to copy wholesale.

All unresolved choices are tracked in [Open Decisions and Roadmap](./Reference/Open%20Decisions%20and%20Roadmap.md).

## Read Next

### Understand the concept

- [Architecture Overview](./Architecture%20Overview.md): the system map, independent roles, communication boundaries, and deployment examples.
- [Resource Model](./Concepts/Resource%20Model.md): what a portable release contains.
- [Finding Worlds](./Concepts/Finding%20Worlds.md): Client startup, addresses, search, and indexing.
- [Platform Services](./Concepts/Platform%20Services.md): shared content, directory, account, and social protocols versus custom gameplay networking.
- [Worlds and Sessions](./Concepts/Worlds%20and%20Sessions.md): how Worlds run alone or online.
- [Identity and Profiles](./Concepts/Identity%20and%20Profiles.md): local Profiles, optional federated accounts, friends, and presence.
- [Collections and Sharing](./Concepts/Collections%20and%20Sharing.md): saved Worlds, Avatars, Items, and visibility controls.
- [Avatars](./Concepts/Avatars.md), [Items and Ownership](./Concepts/Items%20and%20Ownership.md), and [Overlay Apps](./Concepts/Overlay%20Apps.md): portable content.
- [Commerce and Usage Rights](./Concepts/Commerce%20and%20Usage%20Rights.md): what a purchase provides, optional proof of rights, and what survives a store closing.

### Build a Client or World

- [Client and Runtime](./Client%20Platform/Client%20and%20Runtime.md): isolation, multitasking, and Client Profiles.
- [Features and Permissions](./Client%20Platform/Features%20and%20Permissions.md): compatibility versus user consent.
- [Networking](./Client%20Platform/Networking.md): safe binary I/O, transport choice, custom protocols, and reconciliation boundaries.
- [Publishing and Delivery](./Concepts/Publishing%20and%20Delivery.md): hosting, mirrors, integrity, and private retrieval.
- [Message Relay](./Concepts/Message%20Relay.md) and [Resource Relay](./Concepts/Resource%20Relay.md): realtime session messages and participant-content delivery.
- [Graphics and Shaders](./Client%20Platform/Graphics%20and%20Shaders.md) and [Devices and Input](./Client%20Platform/Devices%20and%20Input.md): deeper platform systems.
- [Software Integration](./Client%20Platform/Software%20Integration.md): pairing external programs, scoped channels, and high-rate binary data.

### Review trust and interoperability

- [Security and Privacy](./Trust/Security%20and%20Privacy.md): threats and security invariants.
- [Safety and Moderation](./Trust/Safety%20and%20Moderation.md): protection from people and harmful experiences.
- [Conformance and Governance](./Reference/Conformance%20and%20Governance.md): profiles, registries, tests, and change control.
- [Web Platform Alignment](./Reference/Web%20Platform%20Alignment.md): modern web references, reuse decisions, and missing application capabilities.
- [Glossary](./Reference/Glossary.md): canonical terminology.
