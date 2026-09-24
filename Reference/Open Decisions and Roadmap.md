# Open Decisions and Roadmap

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](./Glossary.md)

> This page separates the project’s stable goals from proposed corrections and technology choices that still require evidence or author approval.

## Status Labels

| Label | Meaning |
| --- | --- |
| **Direction** | A stable goal of the project |
| **Recommendation** | A proposed architectural correction that is not yet a ratified requirement |
| **Candidate** | A technology or mechanism to prototype |
| **Open** | A decision that has not been made |
| **Deferred** | Deliberately outside the current milestone |

## Stable Directions

- Independently implemented Clients and independently hosted Worlds interoperate through open specifications.
- Security, privacy, portability, cross-platform fallback, and user control are first-class goals.
- Worlds, Avatars, Items, and Overlay Apps share a common publication and delivery model.
- A Release may provide one portable path or optional Resource Variants. No mandatory desktop/mobile build split is imposed.
- Clients select compatible content from verified Manifests before heavy loading or code execution. Server assistance is optional and cannot override local validation.
- Custom graphics use a versioned portable profile with Client-side preparation for the local API. Exact languages and toolchains remain candidates.
- Avatars and Items may contain code that runs in self-scoped Resource Sandboxes and reaches World state only through validated interfaces.
- AR experiences are ordinary Worlds using optional presentation and spatial Features. Common access contracts are needed, but environment-mapping, persistence strategy, and multiplayer alignment remain implementation choices.
- Message Relay and Resource Relay are independent session roles. One provider may implement both.
- Providers for identity, content, discovery, relays, sessions, and moderation are replaceable.
- [Architecture Overview](../Architecture%20Overview.md) separates portable data, local execution boundaries, network roles, and their operators. Independence means explicit contracts, not one mandatory process per role or automatic live migration.
- Direct World Addresses work independently of search. No global catalogue, single home World, or domain per World is required.
- Personal Profiles and saved content have explicit visibility controls. Shared references do not transfer Resource access or ownership.
- Independent stores may offer sources, Portable Archives, service access, or combinations, with explicit rights and dependencies. No universal purchase-proof gate, copy-protection guarantee, or blockchain is required.
- A Client may run as an ordinary application or eventually as a device’s primary system shell.
- The architecture supports both simple hosted Worlds and custom authoritative services.
- Networking exposes safe binary I/O, not one mandatory realtime transport or application protocol. Optional Session Profiles define shared interoperability contracts.
- Platform retrieval and supported directory, account, and social roles use shared Client-native contracts, separate from custom gameplay protocols.
- Manifests are readable declarations, not grants. Clients control scoped access and avoid repeated prompts for already approved operations.
- Replication policy, simulation ticks, prediction, and reconciliation remain application or service choices, but they cannot bypass platform security or World authority.
- Modern web standards are the first design reference across subsystems. The long-term platform preserves applicable modern web use cases. A small prototype is not the final feature ceiling.

## Recommended Architectural Corrections

These recommendations refine the original idea where a direct implementation would create privacy, security, or interoperability problems.

| ID | Recommendation | Reason |
| --- | --- | --- |
| R-01 | Make a global account optional and support guest and pairwise identity | Mandatory global identity creates unnecessary tracking and excludes offline use |
| R-02 | Define a small core and move rich systems into Profiles | One first version cannot reliably standardize an engine, identity network, economy, moderation network, and XR operating shell at once |
| R-03 | Add an Application Principal similar to a [web origin](https://html.spec.whatwg.org/multipage/browsers.html#origins) | Storage, grants, network access, updates, and IPC need one security owner |
| R-04 | Separate Features, Limits, Input Signals, Permissions, and Permission Grants | “Capability” previously mixed technical support with user consent |
| R-05 | Use [glTF](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html) for delivered scenes, not the whole World model, and keep [OpenUSD](https://openusd.org/release/intro.html) an optional authoring input | Application semantics need additional contracts, without requiring separate files or another scene tree |
| R-06 | Evaluate [WebAssembly Component Model](https://github.com/WebAssembly/component-model/blob/main/design/mvp/Explainer.md) with narrow [WIT](https://github.com/WebAssembly/component-model/blob/main/design/mvp/WIT.md) host interfaces | Core Wasm alone does not define typed host APIs, lifecycle, resources, or async behavior |
| R-08 | Separate transport topology from authority per state domain | Relaying messages does not prevent cheating or define shared state ownership |
| R-10 | Specify isolation outcomes, not one process per World | Desktop, mobile, browser, and System Clients require different safe implementations |
| R-11 | Separate World execution, presentation, and session-presence lifecycle | One “background” state cannot express all multi-World behavior clearly |
| R-12 | Require baseline materials before custom shaders and keep [Slang](https://shader-slang.org/slang/user-guide/) a candidate | A language does not by itself define a portable render pipeline, and advanced GPU code raises risk |
| R-13 | Standardize semantic Device Broker signals before a new wire protocol | [OpenXR](https://registry.khronos.org/OpenXR/), [HID](https://www.usb.org/hid), [MIDI](https://midi.org/specs), [OSC](https://opensoundcontrol.stanford.edu/spec-1_0.html), and vendor adapters already solve different transport layers |
| R-14 | Promise explicit Item portability levels, not universal behavior | Complex behavior cannot transfer without common physics, interaction, and authority rules |
| R-15 | Show Overlay Apps in Client-controlled frames | Untrusted UI must remain visibly separate from System UI |
| R-16 | State privacy relay assumptions and residual metadata | Relays reduce IP exposure but do not eliminate traffic analysis or operator collusion |

## Decision Register

| ID | Decision | Current recommendation or candidates | Priority |
| --- | --- | --- | --- |
| D-001 | Project charter, use cases, non-goals, and Core 3D boundary | Adopt the MVP below | Critical |
| D-002 | World Address, deep links, and resolver behavior | Domain-based paths or provider-supplied addresses, independent Resource identity, signed resolution, and offline form. Exact syntax remains open | Critical |
| D-003 | Application Principal and publisher security epoch | Resource ID + verified publisher lineage + epoch | Critical |
| D-004 | Manifest encoding, Release ID, signatures, variants, and Publication Record | Evaluate strict readable [JSON](https://www.rfc-editor.org/rfc/rfc8259.html) + [JCS](https://www.rfc-editor.org/rfc/rfc8785.html), with detached signature binding without self-reference, hash-bound files and variants, and pinned dependencies | Critical |
| D-005 | Core 3D Client Profile, Features, Limits, selection, and fallbacks | Manifest-first local selection with optional minimal service negotiation and a baseline drawn from prototype limits | Critical |
| D-006 | World scene and component model | A defined glTF subset and component binding, with optional OpenUSD authoring and no mandatory visual/collision separation | Critical |
| D-007 | Behavior component ABI and host lifecycle | WebAssembly Component Model + versioned WIT interfaces, without ambient general [WASI](https://wasi.dev/) | Critical |
| D-008 | Permission registry, prompt rules, grants, updates, and revocation | Declared maximum + runtime request + scoped handles, with device selection and capture/export boundaries. Expanded declarations need a new Release. Live amendments remain open | Critical |
| D-009 | Optional Session Profiles, descriptors, service roles, messages, and Transport Bindings | Shared contracts where chosen and custom protocols over the same broker API, without a universal realtime transport | High |
| D-010 | Account ID method, migration, recovery, Claims, social graph, and multi-presence invitations | A guest/pairwise base with person-directed Join Requests and session-specific Invitations. Test [AT Protocol](https://atproto.com/specs/atp) ideas without inheriting public repository assumptions | High |
| D-011 | Avatar Core, Humanoid Rig, and Avatar Behavior host API | Prototype a [VRM 1.0](https://vrm.dev/en/vrm1/)-compatible subset plus the common Resource Sandbox | High |
| D-012 | Resource Relay upload, admission, cache, privacy levels, and routing policy | Remote-host fetch and participant upload through one cached relay protocol, with a stronger optional two-party mode | High |
| D-013 | Baseline renderer, portable shader representation, parameter ABI, and compilation | Start with glTF PBR and Client-side preparation. Evaluate constrained [WGSL](https://www.w3.org/TR/WGSL/) and Slang with backend conformance evidence | Medium |
| D-014 | Item attachment, Resource Behavior, World integration, and persistent state | Start with common self-scoped behavior and keep issuer state separate from files. Use the shared commerce model in D-027 | Medium |
| D-015 | Overlay App portable UI and World integration | Client-framed panel plus typed context interface | Medium |
| D-016 | Device signal registry and Adapter packaging | Semantic Broker API over existing transports | Medium |
| D-017 | Moderation feeds, labels, cross-provider block, and reporting | Local controls first, followed by signed provider feeds | Medium |
| D-018 | Governance, registries, extension admission, and certification | Multiple implementations plus open test evidence | Critical before ratification |
| D-019 | Directory search, indexing, discovery metadata, reviews, and reputation | Replaceable directories with public listings and optional content descriptions. Deeper indexing is optional, and restricted results are access-checked | Medium |
| D-020 | Network host API, transport Features, web-origin mapping, credentials, lifecycle, and errors | Evaluate browser-like request, message, stream, and datagram interfaces for bounded binary I/O through a Network Broker with binding-specific cross-origin rules | Critical |
| D-021 | Optional compact pose profile and adaptive delivery contract | Start with independent binary updates, explicit rig mapping, timing, precision, detail changes, and recovery. Keep provider policies replaceable | High |
| D-022 | Modern web use-case coverage and reuse decisions | Maintain [Web Platform Alignment](./Web%20Platform%20Alignment.md) and record direct reuse, adaptation, deferral, and justified exclusions | High |
| D-023 | Platform Service discovery, retrieval, account, directory, and social contracts | Define Client-native bootstrap before World code, with core retrieval first and optional standardized roles next. Specify bindings, schemas, auth, errors, and federation | Critical for retrieval |
| D-024 | Personal data baseline, Collections, audiences, synchronisation, and extensions | Profiles, friends, and saved World/Avatar/Item references with private defaults, explicit sharing, and separate Resource rights. Inheritance, conflicts, migration, and revocation remain open | High |
| D-025 | Cross-World social messaging and portal navigation | Federated recipient-addressed delivery independent of session relays, plus shared Portal semantics with Client-controlled activation. Private invitation handling, message privacy, and lifecycle remain open | High |
| D-026 | Optional source and session-region preferences | Independent coarse preferences and discoverable choices, with fixed hosts still valid. Routing and allocation stay provider-defined. Exact metadata and region naming remain open | Medium |
| D-027 | Commerce offers, Entitlements, Portable Archives, and restoration | Separate deliverables, rights, and service dependencies. Offer optional detached proofs or scoped online checks under chosen issuer trust. Schemas, extensible claims, holder binding, recovery, revocation, account changes, and transfer remain open | Medium |
| D-028 | AR presentation, spatial interfaces, and persistent placement | Use the ordinary World model with separate composition, camera, spatial data, and geolocation access. Exact reference-space, tracking-loss, anchor, storage, and sharing contracts remain open. No universal mapping or alignment algorithm is imposed | Medium |

## Current Concept Pass

Continue from user journeys and service responsibilities before choosing exact schemas or packet fields:

1. **Open the Client and find a World:** direct addresses, chosen directories, saved content, and optional home Worlds. See [Finding Worlds](../Concepts/Finding%20Worlds.md).
2. **Keep a personal identity and belongings organised:** local Profiles, federated accounts, friends, Collections, and audiences. See [Identity and Profiles](../Concepts/Identity%20and%20Profiles.md) and [Collections and Sharing](../Concepts/Collections%20and%20Sharing.md).
3. **Go from a World result to an actual visit:** explain public and restricted sessions, invitations, participant identity, and failure or fallback without binding them to one provider.
4. **Return, switch Clients, or change providers:** clarify what persists, what synchronises, and what remains local or unavailable. [Commerce and Usage Rights](../Concepts/Commerce%20and%20Usage%20Rights.md) now covers purchased files versus service access, backup promises, and optional proofs. Holder recovery and new-account portability remain unresolved.
5. **Use a World in physical surroundings:** [AR presentation: XR and Passthrough](../Client%20Platform/Client%20and%20Runtime.md) reuses World behavior, storage, and networking. Distinguish saved content, physical placement, and shared-space alignment without prescribing one AR implementation.

The technical deliverables below are a later implementation track, not a demand to freeze formats during this concept pass.

## Next Concrete Specifications

Architecture direction is now clearer, but independently built Clients still lack exact contracts. The following are planned deliverables, not completed specifications:

Use [the communication map: 4. What Talks to What](../Architecture%20Overview.md) to assign each contract an owner. Test replacement of compatible implementations, scoped failure, and authority boundaries. Do not standardize provider orchestration or an internal Client plug-in ABI merely because the overview draws separate boxes.

| Order | Deliverable | First proof of interoperability |
| --- | --- | --- |
| 1 | **Publication, Manifest, and Retrieval**: bootstrap, readable schema, canonical bytes, signatures, dependencies, variant selection, bounded retrieval, fallback, and errors | Two loaders use different hosts without provider-specific World code, compute the same Release ID, and load only compatible paths |
| 2 | **Core 3D and Scene Binding**: glTF subset, formats, coordinates, baseline materials, numeric Limits, object references, and component meanings | Two Clients open the same small World from local files or static hosting, without a selection service |
| 3 | **Behavior, Network, and Permission Interfaces**: typed host API, lifecycle, scene operations, bounded binary I/O, grants, and errors | One interaction and one custom networking library run in both Clients without bypassing principal or state-authority checks |
| 4 | **Optional Session and Relay Profiles**: bootstrap, common bindings, authority, compact message schemas, adaptive delivery, upload/admission, and reconnect | Two Clients use one shared profile despite different relay scheduling policies. Hosted and participant-uploaded Avatars both work |
| 5 | **Portable Raster Profile**: distributed representation, renderer interface, compile policy, backend mapping, and fallback | A small shader corpus passes on two implementations/backends, including failure and stale-cache tests |

World Address resolution and publisher/principal identity (D-002 and D-003) are prerequisites for online publication and persistent grants, not solved by the Manifest schema alone. Early local fixtures may use pinned Releases while those contracts are developed.

Start with a tiny World containing one interactive object and a Release with an optional lighter representation. Write valid and invalid fixtures alongside the first schemas. The first three deliverables establish local interoperability. Sessions and programmable graphics follow. Baseline graphics is part of the core, while custom shaders remain outside the initial MVP.

Server-assisted variant selection can be specified after the local path works. It need not introduce remote compilation, mandatory hardware disclosure, or provider-specific builds.

For networking, define the host API and observable delivery properties before fixing a reference binary codec or transport binding. Prototype a custom application protocol and a shared Session Profile over the same broker. Reconciliation can be demonstrated by World code without making its algorithm part of the platform specification.

The first permission fixtures should cover denied and reused grants, selected-camera versus tracking access, revocation, and an update requesting access beyond the previous declaration. Invalid Manifests include duplicate JSON keys, unknown required security fields, and altered signatures. Readable formatting variants should produce the same canonical Release ID under the selected encoding rules. These are planned tests, not yet an implemented suite.

After core retrieval, specify directory queries and account/social operations independently of gameplay. Two providers should work with the same Client contract. Cross-provider friendship needs an additional federation test. Exact runtime update activation and any live permission-amendment mechanism need security review before adoption.

## Recommended Roadmap

### Phase 0: Define the testable problem

- approve the charter, main use cases, and explicit non-goals.
- freeze canonical terminology.
- define trust boundaries and the Application Principal.
- choose two prototype Clients with different engines or codebases.
- write the first conformance scenarios before finalizing formats.

### Phase 1: Local World interoperability

Prove that two independent Clients can:

1. open the same World Address.
2. fetch one immutable Release from two different Mirrors.
3. validate the same Manifest and content hashes, selecting optional variants before heavy loading.
4. render the baseline World scene and component graph.
5. run one portable behavior component through a versioned host API.
6. operate without an account or network.
7. deny undeclared network, filesystem, sensor, and identity access.
8. fall back cleanly when an optional Feature is absent.
9. contain malformed content and a crashing World.
10. pass the same public conformance suite.

### Phase 2: Portable presence and online sessions

- Avatar Core, Humanoid Rig, and self-scoped Avatar Behavior.
- Resource Relay for hosted and participant-uploaded content, including admission limits and caching.
- Session Descriptor, join flow, message envelope, and authority table.
- brokered transport APIs and optional shared bindings, tested alongside a custom sandboxed application protocol.
- compact pose profile with variable-rate/detail delivery and loss recovery, independent of provider scheduling algorithms.
- guest, pairwise, and optional federated identity.
- basic Profile and Collection portability, chosen World Directories, and audience-aware friends and presence.
- reconnect, abuse limits, and privacy-preserving default routing.

### Phase 3: Portable applications and richer devices

- Item appearance, attachment, Resource Behavior, and integration levels.
- Client-framed Overlay Apps.
- Device Broker signals and adapters.
- optional AR presentation and spatial interfaces, with permission, tracking-loss, anchor-restoration, and shared-alignment failure tests.
- custom raster graphics profile.
- richer World Services and persistent state.

### Phase 4: Ecosystem services

- optional Entitlements and commerce, with transparent offers and portable restoration tests.
- federated moderation and reputation.
- advanced compute and ray tracing profiles.
- Semantic Avatar rigs.
- System Client deployment and device-vendor integration.

## Future Creator Guidance (Non-Normative)

The standard does not require creators to separate visual meshes, collision data, interaction data, or runtime state. If a creator chooses separate representations or quality variants, future authoring guidance should recommend keeping their intended behavior synchronized. Dynamic Worlds may generate or update these representations together at runtime.

## Explicit MVP Non-Goals

The first milestone should not attempt to standardize:

- universal deterministic physics.
- a universal replication or reconciliation algorithm, fixed global tick rate, or compulsory realtime transport for every World.
- unrestricted Avatar or Item code with direct authority over World or participant state.
- persistent cross-World Overlay Apps.
- custom shaders, compute, or ray tracing.
- a new universal device transport.
- a global economy or ledger.
- full moderation federation.
- mandatory federated accounts.
- a complete XR operating environment.

Deferring these features protects the larger goal by making the first interoperability claim implementable and falsifiable.

Deferral does not mean permanent loss of applicable web functionality. [Web Platform Alignment](./Web%20Platform%20Alignment.md) tracks the broader target and the evidence needed for each reuse or adaptation decision.

## How to Resolve a Decision

Each decision should produce:

1. a concrete use case and threat model.
2. at least two credible alternatives.
3. a prototype in two independent implementations where practical.
4. failure, migration, and privacy analysis.
5. conformance tests.
6. an explicit recorded outcome and affected-document update.

Each subsystem review also identifies relevant current web standards and records what is reused, adapted, or not adopted, with the reason. Prefer established mechanisms where they fit. Neither legacy compatibility nor novelty alone decides suitability.

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Process: [Conformance and Governance](./Conformance%20and%20Governance.md)
- Canonical terms: [Glossary](./Glossary.md)
