# Platform Services

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> Clients need shared ways to find content, verify it, and use accounts and social services before any World-specific code runs.

## At a Glance

- [Architecture Overview](../Architecture%20Overview.md) maps these roles alongside local execution, content, and session services.
- Platform service protocols are standardized. World gameplay protocols remain developer-defined.
- A Client can open a compatible publication without first downloading a provider-specific networking script.
- Content loading belongs to the interoperable core. Directories, accounts, and social services are optional standardized roles, not mandatory central providers.
- Supporting a role means following its versioned contract, not merely claiming to provide a similar feature.
- A standard request does not necessarily require a separate stateful handshake. [HTTP](https://www.rfc-editor.org/rfc/rfc9110.html) request/response may be sufficient.

## Where the Boundary Lies

| Operation | Shared platform contract | What remains flexible |
| --- | --- | --- |
| Open a World Address | Resolve publication authority and a Release | Which directory or link supplied the address |
| Obtain a Release | Retrieve the Manifest and hash-identified files. Validate versions, requirements, and integrity | Hosts, Mirrors, caching implementation, optional server-assisted variant selection |
| Browse Worlds | Directory query and result formats, pagination, errors, and portable World Addresses | Ranking, curation, UI, and which directories the user trusts |
| Sign in and manage a Profile | Provider discovery, authentication, scoped authorization, Profile operations, sign-out, and recovery interfaces | Provider choice, account UI, and optional guest use |
| Manage friends | Relationship requests and responses, removal, blocking, and permitted presence sharing | Provider implementation and user privacy preferences |
| Send personal messages or social requests | Cross-provider delivery of recipient-addressed messages, Join Requests, and Invitations | Compatible service operators, conversation UI, and notification settings |
| Organise personal content | Collections of Resource references, audiences, synchronisation, and export/import | Folder or list UI, storage implementation, and optional extension data |
| Run a multiplayer World | Safe [host APIs](../Client%20Platform/Networking.md) and any shared Session Profile the World chooses | Game messages, backend, replication, simulation, and reconciliation |

“Any server” means any server implementing the relevant supported contract. It does not mean that a Client can understand an arbitrary server API without an adapter.

The platform distinguishes user-wide friends and account presence from World-specific teams, NPC relationships, or game state. The latter can use the World's own protocol. A World may call approved platform APIs, but it does not replace their credential or consent boundary.

[Finding Worlds](./Finding%20Worlds.md) owns the search and navigation experience. [Collections and Sharing](./Collections%20and%20Sharing.md) defines the proposed baseline for personal data. A compatible account experience may combine several service roles without making one provider responsible for all infrastructure.

[Social messaging: Social Messages Across Worlds](./Identity%20and%20Profiles.md) is independent of World session relays. [Portals: Portals and Navigation](./Finding%20Worlds.md) and UI links use the same Client-controlled navigation boundary. A World controls placement of its shared portal objects, not another user's acceptance or admission.

[Commerce and Usage Rights](./Commerce%20and%20Usage%20Rights.md) proposes a later optional role for offers and rights verification. Participating Clients and services need shared contracts for issuer discovery, scoped authentication, proofs, online checks, extensions, and failures. Basic Resource loading does not require commerce support. Custom rights attributes cannot bypass the Client's network or credential policy.

## Opening Content Without World Code

1. Resolve the address using a supported platform binding and identify the publisher authority.
2. Retrieve a bounded publication record and Manifest, using standardized authentication if the content requires it.
3. Verify the Release and display Client-generated information about its requirements and requested access.
4. Select compatible content, retrieve its files, and validate them through [Publishing and Delivery](./Publishing%20and%20Delivery.md).
5. Start sandboxed behavior. It can then use its declared custom services or an optional shared Session Profile.

Directory use and account sign-in are not prerequisites for opening a public or locally available World. Discovery and permission previews do not run World code. Authentication may use a trusted provider flow, but never requires an unverified World script to obtain the very Manifest that describes that script's authority.

Dynamic service locations remain subject to the Network Broker's destination policy. Discovery is not permission to contact arbitrary endpoints.

[Download-source preferences: Download Sources and Regional Preferences](./Publishing%20and%20Delivery.md) and [session-region preferences: Session Regions and Placement](./Worlds%20and%20Sessions.md) are separate optional inputs to supported discovery or service requests. Providers report available choices or a fixed endpoint. Neither multiple regions nor an additional universal handshake is required.

Requests made before a Manifest is available use a Client-owned bootstrap context with its own bounded network and credential policy. They do not grant network access or account credentials to the future World.

## Required Service Contracts

Each standardized role needs exact definitions for:

- discovery and authenticated endpoint selection.
- protocol versions, request/response schemas, and supported operations.
- credentials and scopes, or explicit anonymous access.
- bounded results, pagination, size limits, and content types.
- cancellation, errors, rate limits, retries, and idempotency where relevant.
- privacy, cache behavior, compatibility, and failure handling.

These definitions are not complete yet. The architecture requires common contracts, while the precise bindings and schemas remain in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md). Provider-side code and data storage layouts are not standardized.

Account and social federation also need provider-to-provider rules: a common Client API alone does not make two providers agree about identity or friendship. [Identity and Profiles](./Identity%20and%20Profiles.md) owns those semantics.

## Trust and Permissions

Platform operations use trusted Client interfaces and scoped service handles. Account credentials and a full friend list do not become available to a World simply because the user entered it.

Reading public directory results is different from sharing private identity or creating a friendship. User-initiated social actions and approved recurring synchronization need clear semantics, not a prompt for every background request. Application access to protected data follows [Features and Permissions](../Client%20Platform/Features%20and%20Permissions.md).

Failures stay scoped: an unavailable directory does not invalidate an already verified World. A failed social service does not grant a World substitute access to account credentials.

## Current Proposals

- Reuse [HTTPS](https://www.rfc-editor.org/rfc/rfc9110.html#section-4.2.2) and existing web request, authentication, and caching mechanisms where applicable. Define the missing platform-specific data contracts.
- Use the common [Resource Model](./Resource%20Model.md) for Worlds, Avatars, Items, and Overlay Apps instead of four unrelated download protocols.
- Evaluate [OAuth](https://www.rfc-editor.org/rfc/rfc6749.html)/[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)-style authorization and [WebAuthn](https://www.w3.org/TR/webauthn-2/)-based sign-in through [Web Platform Alignment](../Reference/Web%20Platform%20Alignment.md), without treating them as solutions to all federation or migration questions.
- Keep standardized platform metadata separate from high-rate gameplay traffic. Readable [JSON](https://www.rfc-editor.org/rfc/rfc8259.html) is suitable to evaluate for the former.

## Related Topics

- Overview: [Start Here](../Start%20Here.md)
- Publication and retrieval: [Publishing and Delivery](./Publishing%20and%20Delivery.md)
- Manifest: [Resource Model](./Resource%20Model.md)
- Accounts and social data: [Identity and Profiles](./Identity%20and%20Profiles.md)
- Application networking: [Networking](../Client%20Platform/Networking.md)
