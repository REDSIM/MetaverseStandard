# Message Relay

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> A Message Relay forwards bounded realtime messages for one online session without becoming the authority for their meaning.

## At a Glance

- It carries participant poses, events, and World messages.
- Account-level private messages, Join Requests, and Invitations use [social services — Social Messages Across Worlds](./Identity%20and%20Profiles.md), independently of this session relay.
- It is separate from [Resource delivery](./Resource%20Relay.md), voice media, and authoritative simulation.
- A third-party Session Service Provider may operate it, or a World deployment may self-host it.
- The [Session Descriptor](./Worlds%20and%20Sessions.md) identifies the relay used by a session.
- Forwarding or ordering a message does not prove that the sender is honest.
- Transport bindings and shared message formats belong to an optional Session Profile; they do not replace the general [network host API](../Client%20Platform/Networking.md).

## Session Contract

This note describes the shared relay contract for Worlds that opt into a standard Session Profile. A World may instead use a custom application protocol over the same brokered networking API. Choosing a different protocol does not remove privacy, authority, or resource limits.

A Session Descriptor needs to identify:

- the authenticated relay operator and endpoints;
- protocol and transport versions;
- participant and channel authentication;
- supported message classes and size limits;
- ordering, reliability, rate, and replay rules;
- reconnect, timeout, and failure behavior.

The relay accepts only session messages defined by the selected protocol or World interface. It is not unrestricted network access for World code.

## Message Envelope

Messages in the shared relay profile use a versioned envelope that identifies, directly or through established channel context:

- session and channel;
- authenticated sender or authority role;
- message type and schema version;
- sequence, timestamp, expiry, or replay information required by that channel;
- payload length and applicable limits.

Sender identity comes from the authenticated session context, not from an untrusted name inside the payload. Receivers reject messages for the wrong session, unknown required schemas, invalid authority roles, or expired replay windows.

The logical envelope does not require repeating long identifiers or text field names in every packet. A binding may establish compact identifiers during setup, while defining their scope, reuse, and reset rules. The exact binary encoding remains open.

## Delivery Channels

Different traffic needs different delivery rules:

| Channel class | Typical data | Expected behavior |
| --- | --- | --- |
| **Realtime state** | Head, hand, or object poses | Latest value matters; stale messages may be dropped |
| **Reliable event** | Join, leave, interaction result | Ordered and retried within strict queue limits |
| **Authority update** | Validated snapshot or state change | Accepted only from the declared authority owner |
| **World-defined** | Typed application message | Uses a declared schema, limits, and delivery class |

Backpressure is explicit. Realtime traffic drops or coalesces stale updates, while reliable queues remain bounded and fail clearly instead of growing without limit.

## Message Classes

Typical classes include:

- high-rate pose and movement samples;
- participant join, leave, and presence events;
- bounded interaction requests;
- shared-state updates from the current authority owner;
- World-defined typed messages.

Voice and other realtime media use a separate Media Relay role because their transport, privacy, and quality requirements differ. Avatar, Item, and other Content Files use [Resource Relay](./Resource%20Relay.md).

## Replaceable Replication Policy

A **Replication Policy** decides who receives an update, when, and at which supported detail level. It may be a replaceable module within the relay or World service, not another mandatory server role.

Interest management chooses recipients; scheduling chooses update frequency and priority. Providers may use distance, zones, application importance, receiver budgets, or other rules. A basic implementation can forward broadly within quotas. Module and implementation versions need not change the wire protocol when they preserve its contract.

The standard does not prescribe these algorithms or one global tick rate. Simulation, publication, relay forwarding, and display may run at different rates. The selected message profile defines enough timing and state semantics for recipients to interpret irregular updates correctly.

Recipient selection cannot grant access to hidden state. Client requests are constrained by session authority and privacy policy; visibility and distance alone are not authorization.

## Current Proposals: Compact Realtime State

For a reference pose profile, evaluate compact binary updates rather than repeated JSON objects:

- agree coordinate systems, units, rig mappings, and codec versions during setup;
- use short numeric identifiers, bounded arrays, and explicitly specified position and rotation precision;
- allow separately decodable groups such as the core pose, body, fingers, and expressions;
- define sample time, sequence, expiry, and the meaning of omitted data;
- identify layout changes and reset interpolation state when an Avatar or schema changes.

Begin with independent updates that a relay can skip safely. If delta encoding is added, it needs identified base states and recovery: a delta from the previous sent update is invalid when that update never reached the recipient. Detail changes and reconnects also need defined resynchronization.

Reducing forwarding frequency is cheaper than decoding and re-encoding every pose for every observer. Authors may supply a small set of compatible quality representations or discardable groups so a relay can select prepared data. Per-recipient transcoding is optional, not assumed free. Additional source representations also cost bandwidth and encoding work; benchmarks should decide the tradeoff.

With end-to-end encrypted payloads, the relay cannot arbitrarily transform state. Any selection uses an explicitly defined visible routing description and independently usable encrypted groups; this exposes some metadata and requires a separate privacy design.

## Delivery Budgets and Timing

- Keep queues bounded and replace expired realtime samples with newer ones before sending.
- Respect negotiated message sizes and current datagram limits. Large required snapshots can use a reliable stream; pose groups should not depend on an unbounded fragmented frame.
- Map messages to the actual transport properties. Streams need framing; datagrams may be lost or reordered. The [QUIC DATAGRAM extension](https://www.rfc-editor.org/rfc/rfc9221.html#section-5) does not fragment datagrams and still applies congestion control.
- Define clock mapping, wraparound, maximum age, and bounded interpolation or extrapolation within the chosen profile. No synchronized global simulation clock is assumed.
- A successful send is not proof that the World applied a command. Application acknowledgements and retry rules belong to its protocol.

Performance evaluation should measure bytes per participant, relay fan-out, CPU, allocations, queue delay, and behavior under loss and slow receivers. Minimum packet size and minimum CPU cost are not always the same design.

## Authority

A Message Relay may authenticate senders, apply limits, sequence messages, and reject malformed traffic. It does not automatically validate movement, purchases, inventory, physics, or other valuable state.

Authority remains assigned per state domain. A simple social World may accept participant-owned state, while a competitive World may use an Authoritative World Server behind or alongside the relay.

## Privacy and Failure

Relayed transport can prevent participants from learning each other's network addresses, but the relay operator observes session and traffic metadata. Direct peer connections are a separate topology and require an explicit privacy choice.

Relay failure affects only the relevant session. Clients need bounded reconnect behavior, clear failure reporting, and isolation from other open Worlds.

Reconnect rules state whether channels resume from a sequence point, require a fresh snapshot, or restart. The Client never replays non-idempotent actions merely because a connection was restored.

## Related Topics

- Session architecture: [Worlds and Sessions](./Worlds%20and%20Sessions.md)
- Network API and transport choice: [Networking](../Client%20Platform/Networking.md)
- Participant content: [Resource Relay](./Resource%20Relay.md)
- Threat model: [Security and Privacy](../Trust/Security%20and%20Privacy.md)
- Terms: [Glossary](../Reference/Glossary.md)
