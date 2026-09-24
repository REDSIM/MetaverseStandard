# Identity and Profiles

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> A user may carry a familiar representation across Worlds without being forced into one global, trackable account.

## At a Glance

- An account is optional for basic use. A Client can create a local Profile and temporary participant identity.
- A user may keep multiple Profiles for different contexts, with user-controlled visibility.
- A Profile may suggest preferred Avatars, while the Client supplies the Avatar used by each World Presence according to its own selection policy.
- Federated Account Providers authenticate users and may offer synchronization, recovery, or additional services. Friends, reputation, and Entitlements need not share one operator.
- Worlds receive the minimum identity needed for a session, not automatically the user’s stable Account ID.
- Display names and Avatars are presentation. They never prove identity by themselves.

## Core Terms

| Term | Meaning |
| --- | --- |
| **Account ID** | Stable identifier used for an optional federated account |
| **Account Provider** | Service that authenticates the user and stores or synchronizes account data |
| **Account Presence** | User-controlled availability and optional shared activity, separate from participation in any one World |
| **Handle** | Human-readable name that can be resolved to an Account ID |
| **Profile** | Identity presentation selected by the user: name, image, chosen metadata, optional preferences, and visibility settings |
| **Participant ID** | Temporary or pairwise identifier presented to one World or session |
| **Claim** | Verifiable statement such as age eligibility, group membership, or issuer-granted usage rights. A valid signature alone does not establish its truth |

A person may control several Account IDs, and one account may contain several Profiles. A Profile is not necessarily public to the whole network. Local Profiles provide representation and preferences even before registration. A federated account adds portable identity, social relationships, and synchronisation.

[Collections and Sharing](./Collections%20and%20Sharing.md) defines the common personal data model: Profile information, friends, saved Worlds, Avatar and Item collections, audiences, and extensible records. These are metadata and relationships, not necessarily hosted copies of the underlying Resources.

## Three Ways to Enter

### Guest

The Client creates a local Profile and a fresh Participant ID. No Account Provider is required. Remote content hosts or session services may still be contacted. The World may limit features that require persistence or abuse prevention.

### Pairwise

The Client derives or obtains a stable identifier that is different for each World or operator. The user can return without exposing one universal identifier everywhere.

### Disclosed account

The user deliberately shares an Account ID, Profile, or selected Claims. This may be needed for friends, purchases, moderation history, employment, or another persistent relationship.

The World should request the least identifying option that supports its purpose.

## Federation Model

Anyone may operate a compatible Account Provider. The standard needs to separate:

- stable account identity from the current provider.
- human-readable Handles from cryptographic identifiers.
- authentication from public Profile data.
- account migration from private-data migration.
- provider recovery from user-controlled key recovery.
- Claims from automatic disclosure of the underlying account.

A domain may be used for discovery and Handles, but owning one specific domain must not be the only way to retain an identity.

[AT Protocol](https://atproto.com/guides/identity) is a useful reference for separating persistent identity, handles, providers, and signed repositories. It cannot simply be copied: its public-data assumptions and current identifier methods do not automatically satisfy private XR Profiles, pairwise identifiers, or provider-independent recovery.

Its [ordinary repositories](https://atproto.com/specs/repository) are public. The [Atproto Spaces alpha](https://atproto.com/blog/atproto-spaces-alpha), announced in August 2026, explores non-public data with access controls, but is explicitly experimental and does not provide end-to-end encrypted storage. It is a candidate to watch, not an adopted private-profile foundation.

## Authentication and Recovery

The eventual protocol should define separate keys or credentials for:

- everyday sign-in.
- signing public Profile changes.
- issuing or presenting Claims.
- provider migration.
- emergency recovery and key rotation.

Clients should support phishing-resistant authentication where available. Recovery must not let a compromised provider silently rewrite identity history for different observers. How this consistency is achieved is an open protocol decision.

## Standard Account and Social Operations

[Platform Services](./Platform%20Services.md) supplies Client-native contracts for supported account and social roles. Sign-in, Profile access, and friend management do not depend on a downloaded World's custom networking code. Supporting an account role does not require the provider to implement commerce, reputation, or every social feature.

The social contract needs defined request, accept, decline, cancel, remove, and block behavior, including authorization, retries, and cross-provider delivery. Mutual friendship and one-way following are distinct relationships, not interchangeable labels. Exact schemas and conflict rules remain open.

Account presence is also distinct from a [World Presence](./Worlds%20and%20Sessions.md). Being in a World does not automatically authorize sharing its location, the active Avatar, or activity with every friend. The user controls disclosure. World-specific teams and gameplay relationships remain application-defined.

### Multiple shared presences

Several Worlds or sessions may be open at once, so Account Presence does not assume one global current World or Avatar. A Profile can show no locations, one location, or several shared World Presences. Each presence has its own visibility and may be active or away. Sharing one does not disclose the others or their count. No visible locations does not prove that the person is offline.

Availability, location sharing, and permission to join are separate. The Client's foreground World is not automatically public or the default invitation destination. Clients that run only one World use the same model with at most one local active presence. Supporting concurrent Worlds is optional.

### Join Requests and Invitations

A **Join Request** means “I would like to join you.” It is addressed to a person through the selected social identity and does not select a World or session. The requester does not need to see that person's locations. Visibility, permission to send requests, and permission to enter are separate controls.

The ordinary flow is:

1. One person sends a Join Request to another.
2. The recipient chooses an eligible session to which they may invite the requester. It need not be their foreground World.
3. The recipient sends an **Invitation** identifying that particular session.
4. The requester chooses whether to follow it. The destination checks its admission rules before connecting them.

A person may also send an Invitation without a preceding Join Request. Directly joining a visible, joinable session is another action. It does not redefine a person-directed Join Request.

An Invitation discloses only its selected destination and is not authority to bypass the session's rules. Being present in a session does not necessarily allow inviting others. Switching focus or leaving later does not silently retarget an existing Invitation to another World. If the invitation has expired or been revoked, or the session is full, closed, or no longer admits the requester, the Client explains that the invitation cannot currently be used. A different destination needs a new choice.

Requests and invitations can be declined or ignored, and social blocking and request limits apply. A refusal need not reveal whether the recipient has private presences. Expiry, cancellation, and whether an invitation survives the inviter's departure need explicit lifecycle rules later. A request alone neither reserves entry nor reveals private locations, and receiving a request or invitation never automatically opens a World.

## Social Messages Across Worlds

Private messages, Join Requests, and Invitations use the federation's account and social services. The selected Profile identifies the sender and social context. The data is delivered through compatible providers, not stored in the Profile's public presentation fields. A provider may offer both identity and messaging or delegate messaging to another compatible service.

These functions remain available independently of the current World and its [Message Relay](./Message%20Relay.md). Closing or switching a World does not change the message recipient or invitation destination. Local World chat may still use the World's own protocol. It is not automatically a private account conversation.

Text messages and structured social actions can share delivery infrastructure, but have different meanings. Receiving an Invitation does not accept it or open its target. Delayed or duplicated delivery does not make an expired invitation valid or authorise an action twice. The Client coordinates handling across the user's signed-in devices rather than opening the same invitation everywhere.

Users control permitted senders, message requests, blocking, and notifications. A World or Overlay App cannot read conversations, send messages as the user, or receive account credentials merely because it is open. Any application integration needs separately scoped access. Client-managed notifications need not expose message contents to the surrounding World.

“Private message” means addressed to selected recipients. It is not by itself a claim of end-to-end encryption or secrecy from the providers. Encryption, offline delivery, retention, multi-device history, deletion, and federation failure handling need a reviewed design before stronger guarantees are made.

The separation between Client-facing operations and provider-to-provider delivery in [ActivityPub](https://www.w3.org/TR/activitypub/) is a useful reference. It does not mean this project has selected ActivityPub or that its messaging privacy requirements are already solved.

## Privacy Rules

- A World receives only identity fields the user or policy has approved.
- Different Profiles should use different Participant IDs by default.
- Contacts, groups, reputation, and Entitlements are not automatically exposed with a Profile.
- A World or Overlay App cannot silently enumerate the user’s friends.
- Profile discoverability, field visibility, friend-list visibility, Collections, and Account Presence have distinct audience choices. A single public/private switch is not sufficient.
- Different Profiles are not automatically advertised as belonging to one account. Sharing the same Account ID can still reveal that connection.
- Cross-World correlation is reduced, not eliminated: voice, motion, Avatar choice, timing, social graph, and IP metadata can still link activity.
- Account synchronization of Permission Grants is off by default because a grant valid on one device may be unsafe on another.

## Client Responsibilities

The Client shows which Profile and Avatar are active in each World, which identity will be disclosed, and which remote operator receives it. Switching Profiles must not merge storage, Permissions, social context, or audit history unless the user chooses to do so.

An Account Provider may make data available, but the Client remains the local policy enforcement point. A provider’s statement about data use is a notice, not proof that the operator behaves honestly.

## Current Proposals

- Require guest and pairwise participation in the base identity model.
- Make federated accounts an optional profile layered above local presence.
- Borrow design lessons from [decentralized identifiers](https://www.w3.org/TR/did/), [WebAuthn](https://www.w3.org/TR/webauthn-2/)/passkeys, [OAuth](https://www.rfc-editor.org/rfc/rfc6749.html)-style scoped authorization, and [AT Protocol](https://atproto.com/guides/identity).
- Avoid placing all private account data in a publicly replicated repository.

Identifier methods, key custody, recovery, migration, Claims, and social federation are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Personal data and visibility: [Collections and Sharing](./Collections%20and%20Sharing.md)
- Shared service contracts: [Platform Services](./Platform%20Services.md)
- Permissions: [Features and Permissions](../Client%20Platform/Features%20and%20Permissions.md)
- Threat model: [Security and Privacy](../Trust/Security%20and%20Privacy.md)
- Commerce: [Items and Ownership](./Items%20and%20Ownership.md)
