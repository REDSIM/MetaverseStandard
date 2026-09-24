# Software Integration

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> External software should be able to exchange data and approved commands with a Client without becoming part of its trusted runtime.

## At a Glance

An **External App** is a program running outside the Client, such as an animation tool, streaming application, automation service, or accessibility utility. It is not an Overlay App installed inside the Client or a Device Adapter for hardware.

The **External App Bridge** is the Client-controlled interface through which these programs connect. It supports more than tracking: commands, events, application data, continuous samples, and larger transfers. Each connection receives only approved access.

- The same interaction model can work locally or over an explicitly enabled network connection.
- Reliable commands and disposable live samples have different delivery rules.
- Pairing an External App does not authorize it to inspect or control every World.
- Binary messages avoid repeated text conversion in frequent updates.
- Existing OSC applications can connect through a compatibility adapter.

This is an optional integration profile, not a requirement for opening an ordinary World. Physical devices and their semantic Input Signals remain covered by [Devices and Input](./Devices%20and%20Input.md).

## What Connects to What

```text
External App
    ↕ authenticated connection
External App Bridge owned by the Client
    ↕ scoped operations and validated data
Approved Client function or specific World / Overlay App
```

The Bridge can expose approved Client functions directly. For application-specific behavior, the receiving World or Overlay App exposes a typed integration interface. The Bridge routes messages to that interface rather than granting arbitrary scene access.

For example, an animation editor may send poses to a preview World. A streaming tool may receive an explicitly exported camera view. An accessibility tool may submit approved actions. None of these connections automatically reveals other open Worlds, account credentials, raw tracking, or private files.

Sending an action is not equivalent to owning its result. The World and its authoritative service still validate gameplay requests. An External App cannot acquire permission to move another participant merely by authenticating to the Bridge. Imported poses are untrusted application data, not replacements for the Client's physical head tracking, safety boundary, or trusted System UI.

## Connection and Permission Flow

1. The user enables an integration and gives the External App the Client's Bridge endpoint through the pairing flow. Local discovery, if supported, produces an untrusted candidate, not an approved application.
2. The Client authenticates the peer and shows its claimed identity and requested operations in trusted System UI. A display name alone is not authentication.
3. The user pairs the External App. The resulting credential identifies this pairing, not the user's metaverse account.
4. Both sides negotiate protocol versions, supported schemas, delivery modes, and Limits.
5. The user authorizes particular operations and recipients. World-side access follows that Resource's Manifest and Permission rules, and the World decides which integration interface it exposes.
6. The Bridge creates scoped channels. Each channel is bound to its authenticated app, operation, direction, recipient, and lifetime. Incoming data is not broadcast to every open World.
7. Revocation or disconnection closes affected channels and reports their state. Reconnection does not expand the previous grant.

App pairing and World authorization are separate. Client-owned actions do not require an arbitrary World to act as an intermediary, but still require their own approval. Switching the foreground World does not retarget an existing channel. Background operation is separately controlled.

General-purpose integration does not mean a remote shell. Arbitrary native code execution, unrestricted file paths, and unrestricted process launching are not baseline Bridge operations. File exchange uses bounded transfers or Client-managed selection under an explicit grant.

The Client can control its own exports, not prevent an independently installed program from using OS access it already possesses. Data delivered to an External App cannot be recalled or assumed to remain local. See [Features and Permissions: Local Processing and Network Export](./Features%20and%20Permissions.md).

## Current Proposal

The proposed first profile uses **WebTransport over HTTP/3**, **Protocol Buffers for control messages**, and a **small versioned binary sample format for frequent updates**. An optional native local path can avoid unnecessary network and copying work without changing permissions or message meaning.

These are concrete implementation candidates, not a finalized wire specification or a claim to be universally fastest. The profile still needs schemas, an interoperable reference implementation, and measurements with representative workloads.

### One common network binding

[QUIC](https://www.rfc-editor.org/rfc/rfc9000.html) provides secure multiplexed streams. Its [DATAGRAM extension](https://www.rfc-editor.org/rfc/rfc9221.html) adds messages that are not retransmitted when lost. [WebTransport](https://www.w3.org/TR/webtransport/) makes streams and datagrams available to web applications through a browser-mediated API.

WebTransport over HTTP/3 is the proposed common binding because both native programs and web applications can implement the same Bridge protocol. A native QUIC endpoint alone is not WebTransport-compatible. A browser page cannot substitute arbitrary QUIC or UDP sockets for the WebTransport API.

As checked on 24 September 2026, the W3C API is a Candidate Recommendation Snapshot, not a final Recommendation. The [HTTP/3 binding](https://datatracker.ietf.org/doc/draft-ietf-webtrans-http3/) remains an IETF draft. An implementation profile therefore pins a tested binding revision and supported implementation combinations. It does not claim interoperability with every browser or every draft revision.

The proposed role arrangement is a Client-owned Bridge listener and an External App connecting to it. The listener binds to loopback by default. LAN access is explicitly enabled for selected interfaces and paired peers, not silently exposed on all addresses. Platforms that cannot host such a listener need a separately specified supported arrangement or report the profile unavailable.

### Secure local and LAN setup

A local address is not a trusted identity. The network binding authenticates the listener with a valid certificate, or a supported certificate-pinning mechanism provisioned through trusted pairing. A fingerprint supplied by the unverified peer alone is not a trust anchor. The binding never disables certificate validation just to make localhost work. Where browser certificate pinning is unavailable, a usable trusted-certificate deployment or another explicitly supported binding is needed.

A web application also needs a secure context and any browser-required local-network approval. The listener checks the browser's Origin against the pairing policy. Origin checks do not authenticate native programs, so every connection additionally proves possession of a scoped pairing credential before opening operational channels.

Initial pairing can use a short-lived, single-use code confirmed in System UI to bind an application key. The exact bootstrap and key-recovery formats remain to be specified. Reusable secrets do not belong in public discovery records or connection URLs. New peer keys or unapproved certificate changes require re-pairing.

Operational channels open only after authentication and authorization complete. Replayable early data, including QUIC 0-RTT, cannot authorize a pairing, change grants, or execute commands. Reconnection and command retries do not bypass replay and deduplication checks.

The Bridge follows the [Network Broker's endpoint and credential rules](./Networking.md). Knowing a port, discovering a service, or accepting its TLS certificate grants no access to sensors, identity, World state, or other peers' credentials.

### Delivery classes

| Class | Proposed use | Behavior |
| --- | --- | --- |
| Control and events | Pairing, subscriptions, commands, results, lifecycle changes | Length-framed messages on bounded reliable streams |
| Latest state | Pose samples, animation parameters, continuously replaced values | Small datagrams with sequence, timestamp, and expiry information |
| Bulk data | Documents, snapshots, imported content | Separate reliable streams with size limits, cancellation, and backpressure |

Control streams are not blocked behind large file transfers. Shared congestion and CPU budgets still apply, so separate streams are not a hard latency guarantee. A command result confirms application handling. Transport delivery alone does not prove that a requested operation succeeded. Retried commands need request identifiers and defined deduplication or idempotency behavior.

Latest-state channels discard obsolete or out-of-order samples. They have bounded queues and negotiated update rates rather than accumulating every missed frame. The receiver can request a lower rate or pause a channel. Sequence rules include a restart generation, and timestamps identify their clock domain. Clock offset estimation, where needed, does not assume synchronized wall clocks.

QUIC datagrams are congestion-controlled, but they have no stream-style receive flow control. The Bridge still limits sender rates, queued bytes, and receiver work. Loss-sensitive state transitions use reliable messages. Delta samples need an identified baseline and a bounded recovery path such as periodic complete samples.

Datagrams respect the actual negotiated size limit. There is no universal 1,200-byte application payload guarantee. Large data moves to a stream or a separately negotiated bounded transfer format, not unlimited datagram fragmentation and reassembly.

A binary [WebSocket](https://websockets.spec.whatwg.org/) binding may provide compatibility where WebTransport is unavailable. It advertises reliable ordered delivery, with possible head-of-line delay, rather than pretending to provide equivalent datagrams. The sender may discard queued stale samples before transmission, but cannot remove bytes already committed to the transport. Features that require unreliable delivery can refuse this fallback.

### Binary messages and custom schemas

[Protocol Buffers](https://protobuf.dev/programming-guides/encoding/) is the proposed control encoding because it provides typed, evolvable messages and generated implementations in multiple languages. Its binary format uses field numbers rather than transmitting field names. This choice does not imply gRPC or require HTTP requests for each command.

Frequent samples use a compact versioned header and packed numeric arrays. The header identifies the negotiated channel and schema, sequence or generation, timestamp, and payload size as needed by its binding. Names, units, coordinate conventions, and schema descriptions are exchanged during setup rather than repeated in every frame. Exact layouts and numeric compression remain to be tested.

Illustrative sizing, not a benchmark: a position of three 32-bit floats and a quaternion of four 32-bit floats contain 28 bytes. One hundred such poses contain 2,800 bytes before headers, identifiers, or transport overhead. Sending each scalar separately wastes framing and scheduling work. Batching related values helps, but batching has a latency budget and this example is too large for some datagram paths.

Custom channels use namespaced schema identifiers and versions. The Client validates bounded descriptors and payloads, and routes only schemas approved for that recipient. Unknown optional fields may be skipped according to the codec. Unknown required operations are rejected, not guessed. Supplying a schema does not install executable code or create new permissions. Parsers enforce message size, nesting, element-count, and processing limits. See [Protocol Buffers security guidance](https://github.com/protocolbuffers/protobuf/security).

[FlatBuffers](https://flatbuffers.dev/white_paper/) remains a possible specialized encoding for large structured data that benefits from in-place reads. It is not selected as a second mandatory control codec. Avoiding unpacking does not remove buffer construction, verification, encryption, transport copies, or application conversions. Its [verifier and limits](https://flatbuffers.dev/languages/cpp/) are necessary when reading untrusted buffers. Any future codec profile needs measured justification and an explicit compatibility contract.

### Optional native local fast path

Two native processes on the same machine may negotiate local IPC instead of the network binding. Unix-domain sockets or OS pipes can carry control messages with OS access restrictions. Larger, frequently reused buffers may use shared memory through mechanisms such as [Windows file mapping](https://learn.microsoft.com/en-us/windows/win32/ipc/interprocess-communications) or [POSIX memory mapping](https://pubs.opengroup.org/onlinepubs/9799919799/functions/mmap.html).

This is an optimization of the same Bridge contract, not a second authority model. The Client authenticates the local peer, applies the same grants, and agrees on framing and delivery properties. Shared memory is local only. A browser page does not gain access to arbitrary native shared-memory objects through this profile.

The fast path defines buffer size and ownership, bounded ring slots or latest-value mailboxes, publication sequence, synchronization, and reclamation. Full buffers cause backpressure or an explicit stale-sample drop, not overwrite of a buffer still being consumed. Revocation stops new operations and closes Client-held mappings and handles. Previously delivered bytes remain outside the Client's control.

An untrusted writer can modify shared bytes after validation. A safe implementation copies a bounded snapshot into Client-owned memory before validating and using it, unless an OS-enforced ownership mechanism prevents further writes for the entire read lifetime. A read-only mapping in the receiver alone does not stop the producer writing. Sequence counters and cooperative locks alone do not establish that security guarantee.

Shared memory can remove a payload transfer between processes when safe ownership permits it. It does not promise zero-copy from producer through the sandbox to the renderer. Ordinary network paths also incur framing, encryption, scheduling, and implementation-dependent copies. Copy counts and latency must be measured rather than inferred from the serializer's name.

### Media and OSC compatibility

High-rate audio, video, GPU surfaces, or very large scientific data may justify specialized optional profiles. They need their own timing, format, access, and resource-lifetime contracts. The general Bridge carries their setup and approved handles or bounded data, without promising that arbitrary raw media will fit its sample channel.

[OSC is already a binary format](https://opensoundcontrol.stanford.edu/spec-1_0.html), not JSON. Existing OSC software remains useful through an adapter that maps addresses and type tags into declared Bridge channels. The reasons for a richer native profile are authenticated pairing, permissions, schemas, delivery classes, and lifecycle control, not an assumption that OSC is inherently slow.

Legacy unauthenticated OSC endpoints are explicitly enabled and restricted. Their adapter neither upgrades an unverified sender into a trusted application nor bypasses the receiving World's rules. Prefer local-only compatibility and visible connection status.

## Failure and Acceptance Criteria

Denied access, unsupported schemas, lost connections, exhausted budgets, and revoked grants produce structured errors or channel-state changes. Stale live input becomes invalid rather than remaining permanently “live.” A failed External App does not stop the Client or unrelated Worlds.

Before accepting the profile, two independent implementations should demonstrate:

- native and browser-app interoperability on the pinned network binding.
- pairing, endpoint authentication, Origin rejection, grant isolation, and revocation.
- bounded behavior under malformed messages, flooding, loss, reordering, and slow consumers.
- correct multi-World routing and no silent destination change on navigation.
- compatible schema evolution, fallback declarations, and reconnect behavior.
- equivalent permissions and results on the native fast path, including shared-buffer mutation tests.
- measured latency, bandwidth, CPU, allocation, and copy costs for small commands, batched samples, and concurrent bulk transfers.

## Related Topics

- Runtime and isolation: [Client and Runtime](./Client%20and%20Runtime.md)
- Physical devices: [Devices and Input](./Devices%20and%20Input.md)
- Consent and export: [Features and Permissions](./Features%20and%20Permissions.md)
- Transport boundaries: [Networking](./Networking.md)
- Remaining specifications: [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md)
