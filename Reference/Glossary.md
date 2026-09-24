# Glossary

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md)

> Canonical terms used throughout the concept architecture.

## A to C

**Account ID**: Stable identifier for an optional federated user account. It is not automatically disclosed to every World.

**Account Presence**: User-controlled availability and optional shared activity, independent of a particular World Presence. It does not imply one globally active World or Avatar.

**Account Provider**: Replaceable service that authenticates a user and stores or synchronizes account data. Account authentication does not imply verification of real-world identity or age.

**Application Principal**: Security identity to which storage, Permission Grants, network policy, identity disclosure, IPC, and update inheritance are bound.

**App Client**: Client installed as an ordinary application on an existing operating system.

**Audience**: People allowed to see a Profile field, Collection, or other personal record. It is distinct from an application's Permission Grant and from access to referenced Resource files.

**Augmented Reality (AR)**: Presentation of virtual content together with the user's physical surroundings. An AR experience is a World using optional Client Features, not another Resource Type or automatic permission to read cameras.

**Avatar**: Portable Resource selected to visually represent a participant in a World Presence.

**Avatar Core**: Minimum anatomy-independent Avatar contract, including root, scale, bounds, viewpoint, anchors, fallbacks, and budgets.

**Authoritative World Server**: World Service that validates and owns selected shared state domains.

**Capability Handle**: Runtime object that provides narrowly scoped approved access. This is an implementer term, not a synonym for Feature or Permission.

**Client**: User-chosen software that discovers, verifies, renders, and runs Worlds while enforcing local policy.

**Client Core**: Small trusted part of a Client that owns principals, policy, lifecycle, grants, and isolation decisions.

**Client Profile**: Named baseline bundle of required Features and minimum Limits.

**Client-framed panel**: Untrusted application UI shown inside a boundary controlled and marked by the Client.

**Claim**: Issuer-attributed statement about an account or subject, such as age eligibility or group membership, with evidence a recipient can verify. A valid signature alone does not prove its truth. Claims are optional disclosures, not mandatory Profile fields or a universal verified status.

**Collection**: User-organised set of saved Resource references, such as favourite Worlds or Avatar and Item choices. It does not itself store the files, prove ownership, or select an active Avatar.

**Content File**: Hash-identified immutable file used by a Release. Technical documents may call it a content blob.

## D to I

**Data Use Notice**: Statement describing how a remote operator claims it will use data. It is not technical proof of compliance.

**Device Adapter**: Isolated component that converts an existing device protocol or vendor API into standard Input Signals.

**Device Broker**: Client-controlled interface that exposes approved device signals and output operations, while mediating adapters, device selection, availability, and safety limits.

**Entitlement**: Issuer-signed statement granting defined rights to a Resource under stated conditions, optionally bound to a particular holder. Acceptance depends on the verifier's trust and usage policy. The statement does not by itself prove authorship or legal ownership.

**External App**: Software running outside the Client, such as an animation tool or accessibility utility. It is not an installed Overlay App or a physical Device Adapter.

**External App Bridge**: Optional Client-controlled interface for authenticated, user-paired external software to exchange bounded data and approved actions with selected Client functions or application interfaces.

**Feature**: Function the Client can technically provide, such as hand tracking or a graphics feature.

**Graphics Profile**: Versioned contract for supported scene, material, or shader behavior, including rendering interfaces, Features, Limits, and fallbacks. It does not name a particular GPU or operating system.

**Handle**: Human-readable account name that resolves to an Account ID and may change independently of it.

**Humanoid Rig**: Optional Avatar profile that gives standard meaning to human-like bones, expressions, gaze, and tracking targets.

**Input Signal**: Versioned semantic input exposed by the Client, including units, timing, validity, and privacy class.

**Invitation**: Offer to join a specific World session, subject to the destination's admission rules. It may be sent directly or in response to a Join Request.

**Item**: Portable Resource integrated into a World, including fixed or dynamically loaded content. Movement, pickup, attachment, and inventory membership are independent options, not requirements.

**Item Instance**: Running copy of an Item with World-defined placement and state, plus a holder or attachments where supported.

## J to P

**Join Request**: Person-directed request to meet or join them, without selecting a World or session. The recipient chooses where to invite the requester.

**Limit**: Numeric boundary of a Client Feature, such as maximum texture size or available tracked poses.

**Manifest**: Immutable, human-readable structured description of one Release, its files, dependencies, requirements, Permissions, budgets, and fallbacks. It is interpreted by the Client, but does not itself grant access.

**Media Relay**: Session service that routes latency-sensitive voice or other realtime media.

**Message Relay**: Session service that forwards bounded realtime poses, events, and World messages. It does not by itself validate simulation or valuable state and is not a general Resource host.

**Mirror**: Independent source serving verified copies of immutable Release data without becoming its Publisher.

**Network Broker**: Client-controlled service that exposes approved network operations while enforcing principal, destination, credential, privacy, lifecycle, and resource policies.

**Overlay App**: User-installed application that can present tools across Worlds under an independent principal and Permission context.

**Participant ID**: Temporary or pairwise identifier used for one World or session.

**Permission**: Registered sensitive operation or data category controlled by the Client.

**Permission Grant**: Revocable decision allowing a particular Application Principal a defined Permission scope and duration.

**Platform Service**: Service implementing a shared Client-facing contract, such as publication retrieval, directory browsing, or account and social operations. A standardized role is not necessarily required for basic use or provided by one central operator.

**Portal**: Optional spatial presentation of a World or session navigation target. The World controls shared placement. The Client controls user-approved navigation and the destination controls admission.

**Portable Archive**: User-retained backup containing a Release's metadata and the files needed for explicitly declared content paths, with applicable terms and optional rights evidence. Independent use also depends on the stated access and holder-recovery conditions. An archive is not a promise of eternal compatibility.

**Profile**: User-selected identity presentation such as a name, image, chosen metadata, optional preferences, and visibility settings. It may be local or associated with a federated account and does not determine one global active Avatar.

**Publication Record**: Signed mutable record connecting a Resource ID and publisher authority to its current Release.

**Publisher**: Authority that controls a Resource and signs its publication updates.

## R to S

**Release**: One immutable published version of a Resource, consisting of a Manifest and referenced Content Files.

**Release ID**: Hash of a Release's canonical root Manifest. The Manifest transitively binds its Content Files by hash.

**Replication Policy**: Replaceable application or service policy deciding which state updates reach which recipients, at what rate and supported detail level. It is not a universal simulation algorithm.

**Resource**: Portable standardized content. Initial Resource Types are World, Avatar, Item, and Overlay App.

**Resource Admission**: Preflight and validation process by which a Resource Relay decides whether it can accept a participant Resource under its size, format, bandwidth, and storage limits.

**Resource Host**: Service that stores and serves publication data or Content Files.

**Resource ID**: Stable identifier for a Resource across Releases.

**Resource Instance**: One running Avatar or Item created from a Release inside a World.

**Resource Behavior**: Code shipped with an Avatar or Item and executed through versioned host interfaces inside a Resource Sandbox.

**Resource Relay**: Session service that fetches or accepts participant Resources, optionally caches them, and serves them without direct viewer-to-origin connections.

**Resource Sandbox**: Nested isolated runtime for one Avatar or Item behavior component, with separate memory, quotas, principal, grants, and self-scoped handles.

**Resource Type**: Registered kind of portable Resource with additional Manifest and runtime rules.

**Resource Variant**: An optional alternative representation or content path declared within one Release, with its own requirements and hash-bound files. It is not a separate publication or update channel.

**Security Epoch**: Explicit principal generation used to stop sensitive grants from silently surviving publisher compromise or recovery.

**Self Handle**: Unforgeable runtime handle that allows Resource Behavior to affect only the explicitly writable parts of its own Resource Instance.

**Session Coordinator**: Session service that creates or discovers session instances and handles their join flow.

**Semantic Rig**: Possible future Avatar profile that describes arbitrary anatomy by meaningful roles rather than species.

**Session Descriptor**: Versioned description of how to join a World session, exchange messages, assign authority, reconnect, and handle failure.

**Session Message Envelope**: Versioned wrapper that binds a message to its session, channel, authenticated sender or authority role, schema, limits, and replay information.

**Session Profile**: Optional interoperable contract for an online experience, defining message schemas, join and delivery rules, authority declarations, and supported Transport Bindings. Implementations claiming it follow its requirements.

**Session Service Provider**: Operator that performs one or more session roles such as coordination, message relay, resource relay, media relay, or authoritative simulation.

**Spatial Anchor**: Reference used to maintain virtual placement relative to the physical environment. Persistence, restoration on another device, and sharing require separately supported mechanisms. An anchor is not a universal geographic address.

**State domain**: Related set of session state with one defined authority policy, such as movement, inventory, or voice.

**System Client**: Client integrated as the primary shell or operating environment of a device.

**System UI**: Client-controlled trusted interface for identity, Permissions, indicators, safety controls, and recovery.

## T to W

**Transport Binding**: Versioned mapping of an application protocol to a particular transport, including setup, framing, and delivery properties. It does not define the World's simulation.

**World**: Portable interactive application or space opened by a Client.

**World Address**: Shareable address that a Client resolves to a World publication and optionally a location or session within it.

**World Directory**: Replaceable search or catalogue service that helps users discover World Addresses and related public or access-approved information. It is not the authority for World identity or safety.

**World Presence**: One participant's runtime presence in a particular running World or session, including its reported Avatar and participation state. Several may coexist without implying a stored per-World Avatar preset or public disclosure.

**World Runtime**: Isolated running copy of a World Release inside a Client.

**World Service**: Optional remote service for session discovery, messages, shared state, persistence, voice, or another declared role.

## Terms to Avoid

- Do not use **orchestrator** for several different jobs at once. Say Client Core for local lifecycle, Session Coordinator for admission, Replication Policy for update delivery, or Authoritative World Server for validated shared state. Provider deployment orchestration is an implementation detail.
- Do not use **capability** alone. Say Feature, Limit, Input Signal, Permission, Permission Grant, or Capability Handle.
- Do not use **asset** as a synonym for Resource. Say visual asset or Content File when referring to a model, texture, or audio file.
- Do not use **ownership** when only an Entitlement, file possession, or runtime control is meant.
- Do not call untrusted application UI a system panel. Use Client-framed panel.
- Do not describe the architecture as fully decentralized when it still relies on chosen providers. Say federated or provider-independent where accurate.

## Related Topics

- Entry point: [Start Here](../Start%20Here.md)
- Role map: [Architecture Overview](../Architecture%20Overview.md)
- Decisions: [Open Decisions and Roadmap](./Open%20Decisions%20and%20Roadmap.md)
