# Web Platform Alignment

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](./Glossary.md)

> Start with the modern web platform. Reuse what fits, adapt what needs to change, and explain every significant gap.

## Design Direction

The long-term platform should support the applicable use cases of modern web applications alongside spatial Worlds. It should not become a narrower application platform merely because content is presented in 3D.

This is a coverage goal, not a claim that the current design already matches the web. The small initial core proves interoperability; later profiles extend it. Necessary functionality is recorded as pending rather than silently excluded.

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
| Addressing, navigation, and network requests | [URL](https://url.spec.whatwg.org/), [Fetch](https://fetch.spec.whatwg.org/) | Reuse addressing and request semantics where possible; define World navigation, publication resolution, credential scope, and cancellation |
| Readable publication and permission declarations | [JSON](https://www.rfc-editor.org/rfc/rfc8259.html), [JSON Canonicalization Scheme](https://www.rfc-editor.org/rfc/rfc8785.html) | Evaluate strict JSON and canonical hashing for [Manifests](../Concepts/Resource%20Model.md); define the schema and signature binding separately from runtime Permission Grants |
| Realtime communication | [WebSocket](https://websockets.spec.whatwg.org/), [WebTransport](https://www.w3.org/TR/webtransport/), [WebRTC](https://www.w3.org/TR/webrtc/) | Expose supported binary transports through [Networking](../Client%20Platform/Networking.md); custom protocols and optional Session Profiles share the same security boundary |
| Isolation, permissions, and authentication | [HTML origins](https://html.spec.whatwg.org/multipage/browsers.html#origins), [Permissions](https://www.w3.org/TR/permissions/), [WebAuthn](https://www.w3.org/TR/webauthn/) | Define principal-to-origin mapping, credential attachment, and binding-specific cross-origin rules; separate account authentication from publisher and application identity |
| Local data, files, and offline use | [Storage](https://storage.spec.whatwg.org/), [IndexedDB](https://www.w3.org/TR/IndexedDB/), [File API](https://www.w3.org/TR/FileAPI/) | Specify scoped persistence, quotas, eviction, user-selected files, and offline behavior; exact APIs and backup guarantees remain open |
| Async work and background lifecycle | [Workers](https://html.spec.whatwg.org/multipage/workers.html), [Service Workers](https://www.w3.org/TR/service-workers/) | Reuse event-driven, cancellable work; adapt to [multi-World lifecycle](../Client%20Platform/Client%20and%20Runtime.md) without assuming unlimited background execution |
| Audio, video, and capture | [Media Capture](https://www.w3.org/TR/mediacapture-streams/), [Web Audio](https://www.w3.org/TR/webaudio/), WebRTC | Preserve media creation, playback, communication, and consent use cases; specify media interfaces, codec profiles, and background routing |
| Portable code and graphics | [WebAssembly](https://webassembly.org/), [WebGPU](https://www.w3.org/TR/webgpu/) | Reuse sandbox and graphics contracts where suitable; add the World host API, scene binding, and tested [Graphics Profiles](../Client%20Platform/Graphics%20and%20Shaders.md) |
| Input, devices, accessible UI, and XR | [WebXR](https://www.w3.org/TR/webxr/), [Pointer Events](https://www.w3.org/TR/pointerevents/), [WAI-ARIA](https://www.w3.org/TR/wai-aria/), [WCAG](https://www.w3.org/TR/WCAG22/) | Adapt permissioned input and semantic accessibility to spatial interfaces; specify focus, text/input methods, localization, non-XR access, and assistive-technology integration |
| AR presentation and physical placement | [WebXR AR Module](https://www.w3.org/TR/webxr-ar-module/), [Raw Camera Access](https://immersive-web.github.io/raw-camera-access/), [Anchors](https://immersive-web.github.io/anchors/) | Adapt optional presentation and spatial interfaces; keep camera images separate from composition; World scripts choose environment interpretation, persistence, and shared-space alignment |

Some references are evolving drafts or contain features with uneven implementation support. A named reference is not evidence that all target devices support it. New networking, device, or graphics functions enter declared Profiles with explicit fallbacks.

## Required Review for Each Subsystem

1. State the user-facing use cases, including accessibility, failure, and offline needs where relevant.
2. Identify applicable current standards and implementation evidence from primary sources.
3. Choose direct reuse, adaptation, deferral, or justified exclusion; record the reason and compatibility level.
4. Describe differences caused by XR, realtime budgets, Resources, or provider independence.
5. Define observable behavior and tests for two independent implementations.

Do not turn useful reference mechanisms into mandatory restrictions on all applications. For example, a standard networking API can coexist with custom application protocols, and a standard permission model does not dictate gameplay.

## Architecture and Role Boundaries

[Architecture Overview](../Architecture%20Overview.md) applies these precedents to the component map. This is semantic reuse and adaptation, not a claim of HTML execution compatibility or adoption of one network stack.

| Reference | Reused or adapted | Not implied / still open |
| --- | --- | --- |
| [HTML origins](https://html.spec.whatwg.org/multipage/browsers.html#origins) and [agents and agent clusters](https://html.spec.whatwg.org/multipage/webappapis.html#agents-and-agent-clusters) | Security identity and observable isolation are different from the implementation's process layout. Adapt this distinction to World Runtimes, nested Resource Sandboxes, and separate Overlay principals | Exact Application Principal construction, host interfaces, and isolation tests remain D-003 and D-007; an Avatar sandbox is not claimed to be a browser Worker |
| [HTTP roles and intermediaries, RFC 9110](https://www.rfc-editor.org/rfc/rfc9110.html#section-3.3) | A role describes responsibility in an exchange, not a permanent one-role-per-program identity. Use this distinction for hosts, relays, operators, and callers | A Resource Relay has metaverse-specific admission, upload, privacy, and session rules; it is not defined merely by calling it an HTTP proxy. Retrieval and relay bindings remain D-023 and D-012 |
| [RTP media topologies, RFC 7667](https://www.rfc-editor.org/rfc/rfc7667.html#section-3.7) | Media forwarding is a distinct responsibility from application state validation. Keep Media Relay separate from Message Relay and simulation authority | This Informational RFC supplies topology vocabulary, not a mandatory RTP/WebRTC binding. Media codecs, transport, privacy, and interoperability tests remain part of D-009 and the broader coverage work |

The HTML Living Standard and established IETF references support the role-based design; they do not prescribe this platform's entire architecture. The role map deliberately leaves internal provider scheduling and Client module packaging implementation-defined. Independence, migration, and actual interchangeability still need contract-specific evidence.

## Coverage Gaps to Keep Visible

The vault already outlines networking, isolation, permissions, graphics, and device input, but their exact interfaces are unfinished. Storage and file APIs, media interfaces, navigation/history, background services, accessible UI and text input, localization, and web-content integration still need dedicated design work.

[Platform Services](../Concepts/Platform%20Services.md) also needs concrete discovery, retrieval, account, and social contracts. Reusing HTTP or an authentication mechanism does not supply the missing World, Profile, friendship, or federation semantics.

There is no blanket exclusion of these use cases. Their exact scope and milestones belong in [Open Decisions and Roadmap](./Open%20Decisions%20and%20Roadmap.md). Hardware-dependent features may be unavailable on a particular device without making the platform's overall design incomplete.

## Social and Spatial References

The web alone does not define the personal experience of a social XR client. For [Finding Worlds](../Concepts/Finding%20Worlds.md) and [Collections and Sharing](../Concepts/Collections%20and%20Sharing.md), use existing social XR products to identify useful tasks, then design portable contracts rather than copying a closed service API.

[VRChat's user guide](https://help.vrchat.com/hc/en-us/articles/28526267258515-Getting-Started-with-VRChat) provides examples of navigation, friends, invitations, Profiles, and favourites. [AT Protocol identity](https://atproto.com/guides/identity) and [Lexicons](https://atproto.com/guides/lexicon) provide different references for replaceable providers and extensible data. Neither by itself defines this project's private Collections, multi-World presence, or sharing guarantees.

### AR reference boundaries

The [WebXR AR Module](https://www.w3.org/TR/webxr-ar-module/) distinguishes optical see-through and video passthrough presentation. Its compositor model does not make camera pixels available to application code merely because reality is visible. [Raw Camera Access](https://immersive-web.github.io/raw-camera-access/) supplies a separate optional interface for applications needing images.

[WebXR reference spaces](https://www.w3.org/TR/webxr/) include reset behavior, while the [Anchors draft](https://immersive-web.github.io/anchors/) describes tracked placement and device-managed, origin-scoped persistence. Our design inference is that ordinary saved coordinates or a local anchor identifier cannot by themselves promise cross-device restoration or shared physical alignment. [AR Worlds — XR and Passthrough](../Client%20Platform/Client%20and%20Runtime.md) keep those application responsibilities explicit.

At this review, the WebXR Device API and AR Module are Candidate Recommendation Drafts, Raw Camera Access is a Draft Community Group Report, and Anchors is an Editor's Draft. These are design references, not universally available APIs or adopted protocol requirements. Exact host interfaces, security scopes, and conformance tests still need independent implementation evidence.

## Related Topics

- Overview: [Start Here](../Start%20Here.md)
- Decisions: [Open Decisions and Roadmap](./Open%20Decisions%20and%20Roadmap.md)
- Compatibility claims: [Conformance and Governance](./Conformance%20and%20Governance.md)
- First worked example: [Networking](../Client%20Platform/Networking.md)
