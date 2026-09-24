# Networking

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> The Client provides safe network building blocks. A World chooses the protocol and simulation logic it runs over them.

## At a Glance

- No single realtime transport, serializer, replication algorithm, or global tick rate is imposed on all Worlds.
- Sandboxed behavior exchanges binary data through the Client's Network Broker, not unrestricted operating-system sockets.
- Standard Session Profiles provide optional ready-made interoperability. Custom application protocols remain possible.
- Choosing a custom protocol does not bypass Permissions, privacy, budgets, or World authority.
- [WebTransport](https://www.w3.org/TR/webtransport/), [WebSocket](https://websockets.spec.whatwg.org/), and other transport bindings are candidates, not universal requirements.

## Platform Protocols Versus World Protocols

Transport freedom applies to application networking, not to making every Client guess how to obtain a World. [Platform Services](../Concepts/Platform%20Services.md) defines shared contracts for address resolution, publication retrieval, directories, accounts, and social functions. Content loading works before any World-specific code runs. Optional service roles follow their standard contracts when implemented.

This page describes the safe building blocks used by World protocols and their optional shared Session Profiles. A chosen platform service or Session Profile still needs concrete compatible bindings. No universal gameplay protocol is implied.

[Software Integration](./Software%20Integration.md) defines a separate optional External App Bridge profile for programs outside the Client. Its proposed WebTransport binding does not make WebTransport compulsory for Worlds. Physical device adapters remain a different boundary in [Devices and Input](./Devices%20and%20Input.md).

## Four Separate Layers

| Layer | Responsibility | Who defines it? |
| --- | --- | --- |
| **Network host API** | Approved connections, byte buffers, asynchronous I/O, limits, cancellation, and errors | The standard, implemented by the Client |
| **Transport binding** | How a protocol uses a particular transport and version, including framing and actual delivery properties | A supported standard or extension |
| **Application protocol** | Message types, serialization, authentication, sessions, and state exchange | A Session Profile or World developer |
| **Simulation and replication** | Prediction, reconciliation, interest management, update frequency, and quality policy | World and service implementations |

This is low-level enough to build different networking libraries, but not so low-level that every World must implement [TLS](https://www.rfc-editor.org/rfc/rfc8446.html), congestion control, or platform socket handling. The network API is separate from the scene and behavior APIs used to apply received state.

## Network Host API

The versioned API exposes only operations supported by the selected transport:

- open an approved endpoint and inspect the connection's delivery properties and Limits.
- issue requests, exchange binary messages, use reliable byte streams, or send datagrams where supported.
- send and receive bounded byte buffers without mandatory text or JSON conversion.
- observe readiness, backpressure, closure, and structured errors.
- cancel pending work, close connections, and reset streams where available.

Transport Features are discovered explicitly. WebSocket messages are not arbitrary byte streams. A reliable stream does not preserve application-message boundaries. A binding states which operations exist and how they map to the wire. It does not silently imitate loss-tolerant datagrams using an ordered stream while claiming equivalent latency.

The API specifies buffer ownership and lifetime, queue budgets, and send outcomes. Accepted into a local queue, delivered by transport, and processed by an application are different outcomes. Implementations may batch calls or reduce copies inside the isolation boundary. Zero-copy behavior is not a portability promise.

World code uses bounded timers and monotonic time from the runtime. Transport timing does not establish a shared simulation clock. Suspending a World pauses or cancels its network work according to the declared lifecycle policy. Background access remains Client-controlled.

## Custom Protocols and Shared Profiles

A World may ship a sandboxed networking library and connect it to its own backend. The library can define message encoding, state replication, acknowledgements, and reconciliation. It uses supported host transports and cannot install a native transport plugin or gain raw socket access merely by being downloaded.

A **Session Profile** is an optional shared contract for a particular online experience. It defines message schemas, join behavior, delivery rules, authority declarations, and supported Transport Bindings. A World choosing that profile follows its contract. A custom protocol does not have to impersonate it.

Each profile needs a common required binding or another explicit guaranteed overlap between its implementations. A list of unrelated optional transports alone does not establish interoperability. This requirement belongs to that profile, not to every World or the offline core.

The Manifest declares required network Features and protocol components. A profile or application protocol may use a fixed declared binding and version, or negotiate them during its own bootstrap. No additional universal session handshake is required for every request or service. An unavailable required transport produces a declared fallback or clear failure, not an undisclosed downgrade.

Custom networking can remain portable when the same sandboxed code runs in different Clients. Reusing built-in presence, Avatar, voice, or relay integration additionally requires implementing their versioned interfaces. A custom payload is not automatically understood by these services.

## Network Broker and Trust

The Network Broker applies the same policy to built-in and custom protocols:

- Bind each connection to an Application Principal, approved destination, transport, and lifecycle scope. A Manifest declaration alone is not a grant.
- Authenticate public service endpoints and protect traffic in transit. Transport choice is not permission to silently use cleartext or disable certificate checks.
- Validate discovery results, redirects, and resolved addresses against destination policy. Loopback, private-network, and link-local access require separately scoped rules. Cloud-metadata endpoints remain blocked.
- Isolate credentials by principal and service. A custom protocol does not inherit another World’s cookies, tokens, or account identity.
- Bound connections, queued bytes, message sizes, receive rates, and work per principal. Backpressure or cancellation is preferable to unbounded memory growth.
- When approved access ends, cancel pending operations and close affected handles. Data already sent cannot be recalled.
- Preserve [participant-content routing](../Concepts/Resource%20Relay.md) and user-visible peer-IP policy. Automatically received Avatars and Items cannot open connections using the host World's grants.

Endpoint authentication does not establish authority over World state. Likewise, an origin-style application identifier does not prove that a remote Client is honest. Application protocols still authenticate participants and state owners.

For reused web bindings, mapping an Application Principal to a web origin, attaching credentials, and respecting server-side cross-origin rules remain explicit open contracts. User approval of a destination does not settle those rules. HTTP [CORS](https://fetch.spec.whatwg.org/#http-cors-protocol) and [WebSocket Origin handling](https://www.rfc-editor.org/rfc/rfc6455.html#section-10.2) need binding-specific definitions. CORS is not a generic rule to copy onto every binary transport.

Direct peer networking needs an explicit privacy choice. It is not a silent fallback when a relay fails. Approved remote services still see connection metadata. Once code has access to data and an approved destination, a generic byte API cannot prove how that service will use the data. See [Features and Permissions](./Features%20and%20Permissions.md) and [Security and Privacy](../Trust/Security%20and%20Privacy.md).

## Prediction and Reconciliation

The platform permits authoritative corrections without prescribing a reconciliation algorithm. World code can send input, receive a server result, maintain bounded prediction history, and update World-controlled state through its scene and behavior interfaces.

Illustrative flow, not a mandatory protocol:

1. World code predicts local movement and sends its input to the server.
2. The server returns authoritative state and identifies which input it processed.
3. World code compares the result with its prediction and corrects or re-simulates according to that World's rules.

Neither the Broker nor Message Relay interprets a packet as a universal command to move a player. The authenticated state owner and World integration decide what can change. Corrections are scoped to that World and cannot alter raw physical tracking, other Worlds, or trusted safety controls.

Fixed-step simulation, rollback, interpolation, and delta-state algorithms are developer choices. A specialized optional profile may standardize one, but the platform does not require it.

## Current Proposals

These are evaluation directions, not required transports:

- **[HTTPS](https://www.rfc-editor.org/rfc/rfc9110.html#section-4.2.2) request/response** for services and Content Files.
- **WebSocket over TLS** for binary message-oriented applications using reliable ordered delivery. The [WebSocket standard](https://websockets.spec.whatwg.org/) supports binary messages without exposing raw network access.
- **WebTransport over HTTP/3** for applications needing streams and datagrams. It carries bytes, not a prescribed pose or JSON format. Its binding and implementation versions need verification before adoption. See [WebTransport](https://www.w3.org/TR/webtransport/) and the [HTTP/3 binding](https://datatracker.ietf.org/doc/draft-ietf-webtrans-http3/).
- **[WebRTC](https://www.w3.org/TR/webrtc/)-based interfaces** where media or peer-connectivity requirements justify them, subject to the same privacy policy.

Core WebAssembly obtains networking through host imports rather than built-in system calls. See its [portability model](https://webassembly.org/docs/portability/). New native transports therefore need an explicit Client extension and interoperability tests. Arbitrary TCP/UDP socket access is not part of the default sandbox contract.

The first reference realtime profile should evaluate compact binary messages and the replaceable replication policies in [Message Relay](../Concepts/Message%20Relay.md). Exact codecs, host interfaces, mandatory support within individual Client Profiles, and transport bindings remain [open decisions](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Runtime: [Client and Runtime](./Client%20and%20Runtime.md)
- Compatibility and consent: [Features and Permissions](./Features%20and%20Permissions.md)
- Sessions and authority: [Worlds and Sessions](../Concepts/Worlds%20and%20Sessions.md)
- Realtime delivery and optimization: [Message Relay](../Concepts/Message%20Relay.md)
