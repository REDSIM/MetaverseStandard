# Web Platform Alignment

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](./Glossary.md)

> Start with the modern web platform. Reuse what fits, adapt what needs to change, and explain every significant gap.

## Design Direction

The long-term platform should support the applicable use cases of modern web applications alongside spatial Worlds. It should not become a narrower application platform merely because content is presented in 3D.

This is a coverage goal, not a claim that the current design already matches the web. The small initial core proves interoperability. Later profiles extend it. Necessary functionality is recorded as pending rather than silently excluded.

The web is a reference, not proof that every problem is solved. Standards evolve, implementations differ, and some mechanisms carry legacy or privacy tradeoffs. Prefer mature, tested solutions where they fit. An older active standard is not obsolete simply because a newer draft exists.

## Reuse at the Right Level

For each subsystem, distinguish:

- **Protocol reuse:** preserve the wire contract so existing servers or tools can interoperate.
- **API reuse or adaptation:** expose the same operations, possibly through a typed sandbox interface instead of JavaScript.
- **Semantic reuse:** preserve principles such as origin isolation, explicit permission, cancellation, or bounded storage while defining an appropriate metaverse interface.

These are not interchangeable compatibility claims. Reusing web principles does not automatically make existing websites run unchanged. Whether and how to embed web content or support web application APIs remains an explicit integration decision.

## Initial Coverage Map

This is a starting inventory, not a complete API catalogue. References identify mechanisms to evaluate, not technologies already adopted. Each technical specification needs a current maturity and implementation review.

| Application need | Web reference | Direction and remaining work |
| --- | --- | --- |
| Addressing, navigation, and network requests | [URL](https://url.spec.whatwg.org/), [Fetch](https://fetch.spec.whatwg.org/) | Reuse addressing and request semantics where possible, then define World navigation, publication resolution, credential scope, and cancellation |
| Readable publication and permission declarations | [JSON](https://www.rfc-editor.org/rfc/rfc8259.html), [JSON Canonicalization Scheme](https://www.rfc-editor.org/rfc/rfc8785.html) | Evaluate strict JSON and canonical hashing for [Manifests](../Concepts/Resource%20Model.md), and define the schema and signature binding separately from runtime Permission Grants |
| Realtime communication | [WebSocket](https://websockets.spec.whatwg.org/), [WebTransport](https://www.w3.org/TR/webtransport/), [WebRTC](https://www.w3.org/TR/webrtc/) | Expose supported binary transports through [Networking](../Client%20Platform/Networking.md). Custom protocols and optional Session Profiles share the same security boundary |
| Isolation, permissions, and authentication | [HTML origins](https://html.spec.whatwg.org/multipage/browsers.html#origins), [Permissions](https://www.w3.org/TR/permissions/), [WebAuthn](https://www.w3.org/TR/webauthn/) | Define principal-to-origin mapping, credential attachment, and binding-specific cross-origin rules, and separate account authentication from publisher and application identity |
| Local data, files, and offline use | [Storage](https://storage.spec.whatwg.org/), [IndexedDB](https://www.w3.org/TR/IndexedDB/), [File API](https://www.w3.org/TR/FileAPI/) | Specify scoped persistence, quotas, eviction, user-selected files, and offline behavior. Exact APIs and backup guarantees remain open |
| Async work and background lifecycle | [Workers](https://html.spec.whatwg.org/multipage/workers.html), [Service Workers](https://www.w3.org/TR/service-workers/) | Reuse event-driven, cancellable work while adapting to [multi-World lifecycle](../Client%20Platform/Client%20and%20Runtime.md) without assuming unlimited background execution |
| Audio, video, and capture | [Media Capture](https://www.w3.org/TR/mediacapture-streams/), [Web Audio](https://www.w3.org/TR/webaudio/), [WebRTC](https://www.w3.org/TR/webrtc/) | Preserve media creation, playback, communication, and consent use cases, then specify media interfaces, codec profiles, and background routing |
| Portable code and graphics | [WebAssembly](https://webassembly.org/), [WebGPU](https://www.w3.org/TR/webgpu/) | Reuse sandbox and graphics contracts where suitable, and add the World host API, scene binding, and tested [Graphics Profiles](../Client%20Platform/Graphics%20and%20Shaders.md) |
| Input, devices, accessible UI, and XR | [WebXR](https://www.w3.org/TR/webxr/), [Pointer Events](https://www.w3.org/TR/pointerevents/), [WAI-ARIA](https://www.w3.org/TR/wai-aria/), [WCAG](https://www.w3.org/TR/WCAG22/) | Adapt permissioned input and semantic accessibility to spatial interfaces, and specify focus, text/input methods, localization, non-XR access, and assistive-technology integration |
| Extensible physical peripherals | [Gamepad](https://www.w3.org/TR/gamepad/), [WebHID](https://wicg.github.io/webhid/), [WebUSB](https://wicg.github.io/webusb/), [Media Capture](https://www.w3.org/TR/mediacapture-streams/) | Adapt selected-device access and change notifications to [Devices and Input](../Client%20Platform/Devices%20and%20Input.md). Protocol descriptors and vendor details do not confer trust or require raw OS access |
| External software and high-rate data | [WebTransport](https://www.w3.org/TR/webtransport/), [QUIC](https://www.rfc-editor.org/rfc/rfc9000.html), [QUIC DATAGRAM](https://www.rfc-editor.org/rfc/rfc9221.html), [Protocol Buffers](https://protobuf.dev/programming-guides/encoding/) | Propose a separate [External App Bridge](../Client%20Platform/Software%20Integration.md) profile with reliable control, compact samples, authenticated pairing, and optional local acceleration. This is not a compulsory World or hardware protocol |
| AR presentation and physical placement | [WebXR AR Module](https://www.w3.org/TR/webxr-ar-module/), [Raw Camera Access](https://immersive-web.github.io/raw-camera-access/), [Anchors](https://immersive-web.github.io/anchors/) | Adapt optional presentation and spatial interfaces, keeping camera images separate from composition. World scripts choose environment interpretation, persistence, and shared-space alignment |

Some references are evolving drafts or contain features with uneven implementation support. A named reference is not evidence that all target devices support it. New networking, device, or graphics functions enter declared Profiles with explicit fallbacks.

## Required Review for Each Subsystem

1. State the user-facing use cases, including accessibility, failure, and offline needs where relevant.
2. Identify applicable current standards and implementation evidence from primary sources.
3. Choose direct reuse, adaptation, deferral, or justified exclusion, and record the reason and compatibility level.
4. Describe differences caused by XR, realtime budgets, Resources, or provider independence.
5. Define observable behavior and tests for two independent implementations.

Do not turn useful reference mechanisms into mandatory restrictions on all applications. For example, a standard networking API can coexist with custom application protocols, and a standard permission model does not dictate gameplay.

## Dynamic Composition and Live Devices

The [HTML embedded-content sandbox](https://html.spec.whatwg.org/multipage/iframe-embed-object.html#attr-iframe-sandbox) and [Permissions Policy](https://www.w3.org/TR/permissions-policy/) are precedents for controlled embedding, not an assertion that an Item is an iframe. Our adaptation lets a World dynamically compose verified Items while keeping discovery, feed formats, placement, and application logic developer-defined. A fixed object need not be a collectible, and loading content does not inherit the parent's grants. No universal spatial-service composition protocol is adopted.

[WebXR](https://www.w3.org/TR/webxr/) distinguishes querying support from requesting an XR session and exposes changes in session input sources. [Media Capture and Streams](https://www.w3.org/TR/mediacapture-streams/) separates device exposure, selection, and access. We reuse those distinctions semantically: Worlds can inspect a permission-filtered runtime view and react to devices appearing or disappearing. A missing peripheral does not ban file retrieval, but an unavailable mode cannot be reported as active. Safe format and execution checks remain separate.

Detailed device discovery is not an unrestricted hardware inventory. [W3C fingerprinting guidance](https://www.w3.org/TR/fingerprinting-guidance/) informs minimal default metadata and scoped access to selected-device details. Unknown custom schemas are reported as unsupported instead of causing automatic driver installation. Exact schemas, notification ordering, and metadata scopes remain D-005 and D-016.

## External Software Protocol Proposal

The initial proposal reuses WebTransport over HTTP/3 for the Bridge network binding, rather than inventing encryption, congestion control, or browser socket access. The application layer adds paired peer identity, channel authorization, Protobuf control messages, and versioned compact samples. It is a new Bridge contract over existing transports, not wire compatibility with OSC or another application's private messages.

The W3C WebTransport API was a Candidate Recommendation Snapshot when reviewed on 24 September 2026. The [HTTP/3 binding](https://datatracker.ietf.org/doc/draft-ietf-webtrans-http3/) was still an IETF draft. QUIC and its DATAGRAM extension are published RFCs, but they do not themselves define the WebTransport session or Bridge schemas. A proposed profile pins tested revisions and checks native/browser interoperability before adoption. It does not make the optional Bridge compulsory for every Client.

Binary WebSocket is a possible explicitly degraded binding, not an equivalent loss-tolerant transport. Native local IPC and shared memory are optional implementation paths under the same authorization contract. Browser applications do not gain native shared-memory or raw QUIC access. Safe buffer ownership and measured performance matter more than promising zero-copy. [Software Integration](../Client%20Platform/Software%20Integration.md) owns these details and D-029 tracks acceptance.

[OSC](https://opensoundcontrol.stanford.edu/spec-1_0.html) is retained through adapters for existing software. It already uses binary messages. We do not replace it on the unsupported premise that it is inherently slow. The new profile adds common pairing, authorization, schemas, delivery classes, and lifecycle behavior.

## Architecture and Role Boundaries

[Architecture Overview](../Architecture%20Overview.md) applies these precedents to the component map. This is semantic reuse and adaptation, not a claim of HTML execution compatibility or adoption of one network stack.

| Reference | Reused or adapted | Not implied / still open |
| --- | --- | --- |
| [HTML origins](https://html.spec.whatwg.org/multipage/browsers.html#origins) and [agents and agent clusters](https://html.spec.whatwg.org/multipage/webappapis.html#agents-and-agent-clusters) | Security identity and observable isolation are different from the implementation's process layout. Adapt this distinction to World Runtimes, nested Resource Sandboxes, and separate Overlay principals | Exact Application Principal construction, host interfaces, and isolation tests remain D-003 and D-007. An Avatar sandbox is not claimed to be a browser Worker |
| [HTTP roles and intermediaries, RFC 9110](https://www.rfc-editor.org/rfc/rfc9110.html#section-3.3) | A role describes responsibility in an exchange, not a permanent one-role-per-program identity. Use this distinction for hosts, relays, operators, and callers | A Resource Relay has metaverse-specific admission, upload, privacy, and session rules. It is not defined merely by calling it an HTTP proxy. Retrieval and relay bindings remain D-023 and D-012 |
| [RTP media topologies, RFC 7667](https://www.rfc-editor.org/rfc/rfc7667.html#section-3.7) | Media forwarding is a distinct responsibility from application state validation. Keep Media Relay separate from Message Relay and simulation authority | This Informational RFC supplies topology vocabulary, not a mandatory RTP/WebRTC binding. Media codecs, transport, privacy, and interoperability tests remain part of D-009 and the broader coverage work |

The HTML Living Standard and established IETF references support the role-based design, but they do not prescribe this platform's entire architecture. The role map deliberately leaves internal provider scheduling and Client module packaging implementation-defined. Independence, migration, and actual interchangeability still need contract-specific evidence.

## Coverage Gaps to Keep Visible

The vault already outlines networking, isolation, permissions, graphics, and device input, but their exact interfaces are unfinished. Storage and file APIs, media interfaces, navigation/history, background services, accessible UI and text input, localization, and web-content integration still need dedicated design work.

[Platform Services](../Concepts/Platform%20Services.md) also needs concrete discovery, retrieval, account, and social contracts. Reusing HTTP or an authentication mechanism does not supply the missing World, Profile, friendship, or federation semantics.

There is no blanket exclusion of these use cases. Their exact scope and milestones belong in [Open Decisions and Roadmap](./Open%20Decisions%20and%20Roadmap.md). Hardware-dependent features may be unavailable on a particular device without making the platform's overall design incomplete.

## Authentication and Optional Claims

[WebAuthn](https://www.w3.org/TR/webauthn-3/#user-verification) explicitly distinguishes authenticator user verification from identifying a natural person. We reuse the separation between account authentication and real-world assertions, without requiring WebAuthn Level 3 or an identity check to create a Profile.

[W3C Verifiable Credentials Data Model 2.0](https://www.w3.org/TR/vc-data-model-2.0/) separates issuers, holders, and verifiers. Its privacy guidance discusses minimal claims such as an age threshold rather than a birth date, and correlation through identifiers, signatures, and status checks. It is a reference for optional Claim handling, not an adopted wire format or age-verification system. Selective disclosure and unlinkability depend on the chosen mechanisms and deployment, not on using the data model alone.

The project deliberately leaves real-world checking methods and verification-provider selection outside the metaverse protocol. [Identity and Profiles: Identity and Age Checks Are External Policies](../Concepts/Identity%20and%20Profiles.md) owns the policy boundary. Generic Claim exchange, if specified later, still needs interoperability and privacy tests under D-010. No mandated legal identity, age threshold, human-uniqueness registry, or global trust list is introduced.

## Social and Spatial References

The web alone does not define the personal experience of a social XR client. For [Finding Worlds](../Concepts/Finding%20Worlds.md) and [Collections and Sharing](../Concepts/Collections%20and%20Sharing.md), use existing social XR products to identify useful tasks, then design portable contracts rather than copying a closed service API.

[VRChat's user guide](https://help.vrchat.com/hc/en-us/articles/28526267258515-Getting-Started-with-VRChat) provides examples of navigation, friends, invitations, Profiles, and favourites. [AT Protocol identity](https://atproto.com/guides/identity) and [Lexicons](https://atproto.com/guides/lexicon) provide different references for replaceable providers and extensible data. Neither by itself defines this project's private Collections, multi-World presence, or sharing guarantees.

### AR reference boundaries

The [WebXR AR Module](https://www.w3.org/TR/webxr-ar-module/) distinguishes optical see-through and video passthrough presentation. Its compositor model does not make camera pixels available to application code merely because reality is visible. [Raw Camera Access](https://immersive-web.github.io/raw-camera-access/) supplies a separate optional interface for applications needing images.

[WebXR reference spaces](https://www.w3.org/TR/webxr/) include reset behavior, while the [Anchors draft](https://immersive-web.github.io/anchors/) describes tracked placement and device-managed, origin-scoped persistence. Our design inference is that ordinary saved coordinates or a local anchor identifier cannot by themselves promise cross-device restoration or shared physical alignment. [AR Worlds: XR and Passthrough](../Client%20Platform/Client%20and%20Runtime.md) keep those application responsibilities explicit.

At this review, the WebXR Device API and AR Module are Candidate Recommendation Drafts, Raw Camera Access is a Draft Community Group Report, and Anchors is an Editor's Draft. These are design references, not universally available APIs or adopted protocol requirements. Exact host interfaces, security scopes, and conformance tests still need independent implementation evidence.

## Related Topics

- Overview: [Start Here](../Start%20Here.md)
- Decisions: [Open Decisions and Roadmap](./Open%20Decisions%20and%20Roadmap.md)
- Compatibility claims: [Conformance and Governance](./Conformance%20and%20Governance.md)
- First worked example: [Networking](../Client%20Platform/Networking.md)
