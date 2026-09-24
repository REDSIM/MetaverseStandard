# Publishing and Delivery

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> Publishers choose where content is hosted, while Clients verify what they receive independently of the host.

## At a Glance

- No central content host is required.
- [Standard retrieval contracts](./Platform%20Services.md) let Clients obtain and inspect content before running any World-specific code.
- A Publisher signs the publication record. Any Resource Host or Mirror may store the immutable files.
- Content hashes make mirrors interchangeable for integrity, though not automatically trustworthy for privacy or availability.
- Participant Avatars and Items normally pass through a [Resource Relay](./Resource%20Relay.md), whether they come from a Resource Host or directly from the participant's Client.
- Private and paid content can control access, but an open Client cannot guarantee that decrypted content will never be copied.

## Actors

| Actor | Responsibility |
| --- | --- |
| **Publisher** | Controls a Resource, authorizes its Releases, and selects its current Release |
| **Resource Host** | Serves publication records, Manifests, or content files |
| **Mirror** | Serves verified copies without becoming the Publisher |
| **World Directory** | Helps users discover addresses. It is not the authority for content |
| **[Resource Relay](./Resource%20Relay.md)** | Fetches or temporarily accepts participant Resources and serves them without direct viewer-to-origin connections |
| **Client Cache** | Reuses already verified content on the local device |

These roles may be combined by one service, but the protocol must not require that combination.

A World Directory may show publisher identity, labels, reviews, popularity, and moderation signals. These help users choose where to go, but they do not replace sandboxing: reputation can be wrong, manipulated, or outdated. Users may choose among several directories.

[Finding Worlds](./Finding%20Worlds.md) owns discovery metadata and indexing policy, including optional descriptions of public content inside a World. Mutable discovery summaries are separate from immutable Release Manifests. A search result never substitutes for publication verification.

## Publication Flow

1. The creator assigns a Resource ID and publisher authority.
2. Tooling builds a Manifest and content files for one immutable Release.
3. The Publisher signs a Publication Record that selects the Resource's current Release.
4. Files are uploaded to one or more Resource Hosts.
5. Directories and other users may advertise additional Mirrors.
6. A Client resolves a World Address, verifies the publication and Manifest, and performs [local compatibility selection: Feature Negotiation](../Client%20Platform/Features%20and%20Permissions.md).
7. It retrieves the selected Content Files from valid sources, verifies every hash, and applies the validation pipeline below.

An authorized Mirror can serve identical files without becoming the Publisher or obtaining a new publication signature. Public reachability alone does not grant redistribution rights. Those follow the applicable terms. Altered bytes fail verification against the original Release's hashes.

### Universal and specialized delivery

The same protocol serves a Release with one portable path or several [Resource Variants: One Release, Optional Variants](./Resource%20Model.md). Files may be fetched individually or packaged in archives. The format should allow the Manifest and selected content to be retrieved without expanding every alternative.

Server-assisted selection is optional. A service returns a reference to a declared variant and its hash-bound files, not different unchecked bytes under the same Release ID. An author-generated conversion published as a new variant requires a new Release. Client-side compilation and transcoding instead produce disposable local caches.

This leaves hosting simple: static file storage is sufficient, while specialized services may optimize delivery without becoming the compatibility authority. Standard addressing, metadata, retrieval, and error contracts are still needed. “Static” does not mean that every host invents a different bootstrap API.

## Download Sources and Regional Preferences

The Client may offer **Automatic** download routing or a preferred service region. This chooses where files come from, not which World session the user joins. A nearby content source and a distant session are a valid combination.

Source discovery or an existing service request may carry a coarse preference when supported. The service can offer suitable Hosts, Mirrors, or delivery endpoints, or report that no regional choice is available. A static host or a single self-hosted endpoint remains valid. No extra stateful handshake or multi-region deployment is required.

The common discovery model should be able to describe alternative sources, the files they can serve, optional region information, and relevant access or availability restrictions. Sources and their current locations belong to discovery metadata, not the immutable Release Manifest. A delivery service may choose an endpoint internally without exposing several URLs. Unknown location stays unknown rather than being presented as a verified region. Exact fields and region naming are deferred.

Clients and services choose their own routing algorithms. Automatic selection may consider approved source availability, observed performance, cache reuse, and local policy. Geographic proximity alone is not a performance guarantee. An already verified local file need not be downloaded again merely because the preferred region changes.

Regional routing preserves the selected Release and Content File hashes. A different source is not permission to substitute content, forward credentials to another operator, or bypass destination checks. Participant Avatars and Items still follow [Resource Relay](./Resource%20Relay.md) routing. A closer personal host is not a silent shortcut around it.

A preferred region is a hint, not proof of where every copy is stored or processed. A strict geographic restriction is a separate policy: if it cannot be satisfied, report the conflict instead of silently treating it as a soft preference. Coarse selection does not require device location or GPS access.

For HTTP delivery, reuse applicable [HTTP semantics](https://www.rfc-editor.org/rfc/rfc9110.html) and [caching rules](https://www.rfc-editor.org/rfc/rfc9111.html). The proposed preference metadata adds no claim that HTTP already defines this project's region-selection contract.

## Participant Resource Delivery

Direct viewer-to-origin downloads expose network addresses and enable participant tracking. The canonical [Resource Relay](./Resource%20Relay.md) model therefore supports both remote-host retrieval and session-scoped upload from an owner's Client through the same immutable Release format.

The dedicated note defines delivery flow, Resource Admission, caching, fallbacks, privacy boundaries, and the relationship to [Message Relay](./Message%20Relay.md).

## Validation Pipeline

Untrusted content passes through bounded stages:

1. bound Manifest size, parse it safely, and verify its schema, publication authority, signatures, and Release ID.
2. inspect requirements, Permissions, dependencies, and budgets, then select a compatible content path.
3. retrieve selected files with transfer limits and verify their hashes.
4. enforce dependency, decoded-size, and compression limits before and during expansion.
5. parse in isolated, memory-limited workers and validate actual content, references, and budgets.
6. compile or transcode in isolated Client services, using the [shader preparation flow: Compilation and Isolation](../Client%20Platform/Graphics%20and%20Shaders.md) where relevant.
7. store native caches as disposable local artifacts.
8. activate only through the appropriate runtime policy, or use a declared fallback on failure.

Revocation or a security advisory may block a Release locally, but an immutable Release itself is never rewritten.

## Private and Paid Releases

Private delivery can authenticate the requester and protect content in transit or storage. Optional [Entitlements](./Commerce%20and%20Usage%20Rights.md) record issuer-granted usage rights separately from the files. Delivery access, permission to wear an Avatar, and permission to redistribute it are different decisions. None is inferred merely from a cache hit.

Once an authorized open Client decrypts a file for rendering, a modified Client can potentially extract it. Documentation and marketplaces must not promise absolute prevention of copying.

Public mirroring also means a Publisher cannot guarantee deletion of all old copies. Updating the Publication Record or revoking a Release can stop normal Clients from selecting it, but cannot erase data already distributed.

## Cache and Portable Archives

A Client Cache is disposable storage for reuse, not a purchase record or a guaranteed complete backup. Its files may be evicted, lack dependencies or variants, or include only device-specific compiled artifacts. Clients may protect cache storage as described in [Commerce and Usage Rights: Discourage casual copying without promising DRM](./Commerce%20and%20Usage%20Rights.md), without treating that protection as a defense against all extraction.

A **Portable Archive** is an explicit user-retained export. It preserves the original Manifest, publication verification material, and the Content Files and pinned dependencies for its declared content paths, together with applicable terms and any rights evidence needed for the promised restoration. It can include one, several, or all variants, but reports its coverage and any remaining service requirements. Device-specific caches alone are not portable delivery data. Editable source files are included only where offered.

Export checks completeness rather than saving only links to files on the seller's host. Explicit backups are not automatically evicted with caches. Import verifies hashes, signatures, compatibility, and policy using the same bounded validation pipeline. Archive expansion also rejects unsafe paths and excessive sizes. Local possession does not bypass the sandbox or authorize execution.

Archived data and optional rights records remain separate from account secrets. A reusable or relayed content package never includes the buyer's private authentication keys. [Commerce and Usage Rights: Make independence a testable promise](./Commerce%20and%20Usage%20Rights.md) defines the stronger conditions for restoration without the original services, including rights and holder recovery.

Where the terms allow rehosting, the user can serve the same verified Release from another host or supply it through a Resource Relay without claiming to be its original Publisher. Backup, session viewing, and public redistribution are distinct uses. The standard does not infer all three from an export option.

## Availability and Abuse

- Clients may use multiple sources and retry without weakening verification.
- Hosts and relays may rate-limit abuse but should expose clear failure reasons.
- Dependencies are pinned so an update cannot silently replace code underneath a Release.
- A failed optional participant Resource produces a placeholder, not a direct unsafe fetch.
- No global certification body is needed for integrity. Trust lists and reputation may still help users choose reliable services.

## Current Proposals

- Content-addressed storage for immutable files.
- A signed, mutable Publication Record for publisher authority and the current Release.
- [TUF](https://theupdateframework.github.io/specification/latest/)-style key roles and recovery concepts for secure updates.
- [HTTP](https://www.rfc-editor.org/rfc/rfc9110.html)-compatible Resource Hosts and Mirrors for easy deployment.
- Ordinary cached relays plus an optional high-privacy retrieval profile.
- Session-scoped participant uploads as an alternative to permanent Resource hosting.

Exact formats, directory federation, privacy levels, paid-content protocols, and World Address resolution remain in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Data model: [Resource Model](./Resource%20Model.md)
- Threats: [Security and Privacy](../Trust/Security%20and%20Privacy.md)
- Identity: [Identity and Profiles](./Identity%20and%20Profiles.md)
- Participant delivery: [Resource Relay](./Resource%20Relay.md)
- Purchases, proofs, and preservation: [Commerce and Usage Rights](./Commerce%20and%20Usage%20Rights.md)
