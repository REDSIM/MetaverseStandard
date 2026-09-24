# Commerce and Usage Rights

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> A purchase should say what you receive, what you may do with it, and what still depends on someone else's service.

## At a Glance

- Independent stores can sell editable sources, ready-to-use files, service access, or a combination.
- A **Portable Archive** is a deliberate backup of a Release for declared compatible uses. A download link, Collection entry, or disposable cache is not that backup.
- Optional **Entitlements** record rights granted by an issuer. They are separate from content files, not certificates proving that a work is original or impossible to copy.
- Basic Resource use has no universal purchase-proof requirement. Communities and services may apply their own clearly stated acceptance policies.
- Store-independent use and service-dependent access are both supported, but buyers need to know the difference before paying.

This model applies to Avatars, Items, Worlds, and Overlay Apps. It is a concept proposal, not a finished payment or credential protocol.

## What a Store Sells

| Deliverable | What the buyer receives | What it does not imply |
| --- | --- | --- |
| **Source files** | Editable projects, models, textures, or code listed in the offer | A ready-to-run Release, included third-party tools, or unrestricted redistribution |
| **Portable Archive** | Files needed to restore a specified Release and its declared content paths in a compatible Client | All future variants, perpetual compatibility, or a working external backend |
| **Service access** | Use through a named service under stated conditions | Exportable files or continued access after the service closes |

These can be combined in one product. A ready-to-use Avatar does not need to include its authoring project. “Public,” “free,” and “downloadable” are not substitutes for usage terms.

An offer explains separately:

- permitted uses: wearing, modification, backup, use across the buyer's accounts, transfer, and redistribution;
- delivery to other participants so they can see the content, including relay caching, versus giving them independent reuse rights;
- duration, required accounts and services, export availability, and what survives delisting or service closure;
- included Releases, updates, support, compatibility, and any third-party dependencies;
- refund, revocation, recovery, and dispute conditions.

The buyer can retain the terms accepted at purchase. A later listing edit does not silently replace that recorded agreement. Machine-readable summaries help Clients explain an offer; they do not replace its full terms or resolve legal disputes.

## Buying and Restoring

The store, payment provider, rights issuer, Account Provider, and Resource Host may be different operators. None needs to be the ecosystem's central authority.

The proposed flow is:

1. Inspect an offer and its delivery, rights, and dependency summary.
2. Confirm the seller and transaction through a trusted payment flow, not an arbitrary Avatar script.
3. Receive the purchased access or files, a receipt, the accepted terms, and an Entitlement where offered. Interrupted fulfillment can resume without a second charge.
4. If export is included, save a Portable Archive outside disposable caches. A Client can check completeness and explain what can be restored.
5. Import on another compatible Client using normal validation and the applicable rights policy. Importing does not automatically execute code or restore old device Permission Grants.

A payment receipt is not automatically a portable Entitlement. The future commerce profile needs explicit issuance, retry, refund, and failure behavior; payment processing itself can use existing independent services.

## Current Proposals

### Start with a simple portable offer

For the first commerce implementation, recommend ready-to-use downloads with explicit personal backup rights and no recurring shop check for normal local use. Source files can be a separate deliverable; optional Entitlements support participating communities without becoming a platform-wide gate. Service-only products remain possible but are clearly labelled as such.

Build on the existing Release, hosting, and import mechanisms, with a small separate offer and rights layer. Defer exclusive resale, complex subscription rules, and privacy-preserving multi-account credentials until their recovery and interoperability behavior is tested. This is a recommended starting point for implementers, not a restriction on what stores may sell.

### Separate files from proof of rights

Keep three different statements separate:

- **Publication:** this Publisher authorized this exact Release.
- **Entitlement:** this issuer granted a stated subject particular rights to a Resource or specified Releases under recorded conditions.
- **Holder verification:** the current user can demonstrate that the claimed right applies to them in this context.

Entitlements are detached, signed records. Selling to another buyer does not change the Release, add buyers to its Manifest, or require everyone to download another copy. A host can keep the same immutable files and distribute small, private rights records separately. A public-use offer can publish common terms without maintaining a list of all wearers.

A verifier checks the issuer, scope, subject, and applicable conditions, not merely whether any signature is mathematically valid. If a store issues on a Publisher's behalf, that authority needs to be established by delegation or the verifier's explicit trust policy. Signing the same file under a different name does not reproduce the original issuer's grant. It can still create a new publication that the platform cannot automatically identify as a copy.

For holder-bound rights, the user needs to demonstrate control of the relevant account or key in a way that resists replay. Merely copying somebody's signed receipt is not enough; backing up one's own Entitlement is legitimate and expected. This is separate from decrypting or displaying an Avatar: viewers do not receive the wearer's private identity key. Exact credential, holder-binding, and privacy mechanisms remain open.

Worlds, services, and Clients enforce only their chosen policies; World creators are not required to become copyright judges. A participating community might accept rights from selected creators or stores. Another may require no purchase proof. The result is local acceptance, not a global verdict on authenticity.

Distinguish absent proof, an unrecognized issuer, temporarily unavailable verification, invalid proof, and expired or revoked rights. None alone proves theft. A rejected Avatar or Item should have a clear explanation and a fallback where the World permits one.

Entitlements do not grant camera access, network access, or authority over World state. Those remain [Client Permissions](../Client%20Platform/Features%20and%20Permissions.md) and World integration decisions.

### Online checks without a query from every viewer

A store may check its own purchase records when the buyer selects an Avatar. The Resource Host can also perform this role, but hosting files and issuing rights remain distinct responsibilities.

The decision concerns permission to use the Resource, not necessarily a purchase: a gift, subscription, creator grant, or public offer may establish the right too.

The proposed service-dependent path is:

1. The buyer authenticates to the selected issuer through a Client-controlled flow. A display name or supplied Account ID alone is not authentication.
2. The issuer provides a bounded use proof, or a reference that an authorized verifier can check online. The result identifies the permitted action, Resource or Releases, holder, validity, and intended recipient or session as appropriate.
3. A Client or World service applying that rights policy verifies the proof. It contacts an accepted issuer when the policy needs an online check; other participants can use issuer-signed evidence or a decision from a verifier they explicitly trust. A participant's unsupported “the store said yes” is not evidence.
4. File access is handled separately. If delivery is restricted, the Resource Relay receives only the scoped authority it needs to retrieve and serve the content, not the buyer's full store credentials. Viewer downloads still follow relay routing.

Checking a proof does not require redownloading a cached Avatar. Conversely, cached files do not satisfy a required rights check. Proof lifetime and any cached verification result follow the declared policy and do not extend a proof beyond its expiry; longer reuse reduces load but delays discovery of revocation. Denial, expiry, or an unavailable issuer produces a stated fallback or access failure, not an accusation of piracy or an unlimited silent extension.

There is no default public endpoint for asking what any named person bought, and no compulsory request from every viewer to the Avatar's host. Approved online checks can still reveal usage to the issuer or verifier; relay routing alone does not hide that metadata. Endpoint discovery and credentials follow the [platform service](./Platform%20Services.md) and Network Broker boundaries, including checks against arbitrary destinations supplied by another participant.

[OAuth token introspection](https://www.rfc-editor.org/rfc/rfc7662.html) is a reference for authorized online status queries and their caching/privacy tradeoffs, not an Avatar licensing protocol to adopt unchanged. Authentication, entitlement meaning, and content delivery still need separate contracts.

### Extensible claims and chosen trust

An issuer may attach additional claims, such as a creator-declared edition, customization allowance, or service membership. Extensions have distinct names, versions, types, and signed meaning. An unsigned display label is not a verified right. Unknown optional claims can be ignored; an unknown condition required for authorization cannot be treated as satisfied. Extensions never create Client Permissions or arbitrary executable verification code.

A community chooses which issuers and delegated verifiers it accepts for which claims. Trust in a file host for reliable delivery is not trust in its copyright assertions. A self-hosted creator can be an accepted issuer; a large service can still make mistakes. The core does not assign automatic trust by provider size or prohibit self-hosting.

These policies let independent communities develop stricter or looser systems without imposing a global certification authority. They do not guarantee that reputation will eventually eliminate infringement, or that the same claim will be accepted everywhere.

### Make independence a testable promise

Store independence means the original shop is no longer required; other disclosed dependencies may remain. **Offline restoration** is a stronger promise: the saved Portable Archive and any separately protected holder material support the declared local function without an online shop, license server, or Account Provider. This does not mean that multiplayer works without a session service.

The preservation path includes:

- the original Manifest, publication verification material, and every Content File and pinned dependency needed for the declared paths;
- portable scene and behavior representations, not only device-specific compiled caches;
- accepted terms and any Entitlement and issuer verification history required by the promised verification policy;
- any necessary content-access material under the buyer's control, not encrypted files whose only key remains on a vanished server;
- a disclosed way to preserve or recover any required holder credentials separately and securely, without putting account secrets into publicly relayed content.

No recurring store login, license heartbeat, or future issuer key fetch can be necessary for that promised independent path. An offer requiring a fresh online revocation check remains service-dependent for that check; a saved signature does not reveal future revocation. Cached key history also cannot establish that an issuer was trustworthy or a key never compromised.

The practical test is to disable the original services and restore on a clean compatible Client with only the documented backup and recovery material. Failure reveals a dependency to remove or disclose. Archive mechanics and import validation belong to [Publishing and Delivery — Cache and Portable Archives](./Publishing%20and%20Delivery.md).

Independence preserves the stated function, not every future environment. Old formats may need conversion, vulnerable code may be blocked, and a community may change its admission policy. Updates, online inventories, remote World logic, and support survive only if their own services or preservation arrangements survive.

### Changing accounts is not the same as transferring a purchase

Moving between Account Providers while retaining the same identity is distinct from using a genuinely new Account ID. Copying files does not rewrite the subject of an Entitlement.

An offer can allow the buyer to use several accounts or move to a new one. Where proof is required, that needs an explicit mechanism: issuer-assisted rebinding, or a portable holder identity and delegation accepted by verifiers. The former depends on the issuer; the latter needs protected keys, recovery, and privacy rules. No provider-independent rebinding mechanism has been selected yet, so new-account portability is not an automatic promise.

Transfer to another person and resale are separate rights. An authority can track a unique online right, but cannot prove that a seller deleted previously saved offline files. A product cannot honestly promise both unrestricted permanent offline use and technically enforced removal of all old copies after resale or refund.

### Discourage casual copying without promising DRM

Recommended Client behavior separates viewing from acquiring content. A viewer cache does not automatically become “my purchased Avatars,” and copying its contents does not create an accepted issuer Entitlement. Where a rights policy applies, verify the presenter instead of trusting their Client's claim.

Implementations may keep caches in managed storage and use operating-system storage protection to discourage casual file reuse. Runtime Releases can omit editable source projects and development-only data. Neither measure is a portable anti-ripping guarantee: rendered data remains extractable, compiled content can be inspected, and extraction tools can lower the effort again.

Authorized export uses an explicit backup path instead of forcing buyers to extract their own purchases from caches. There is no mandatory cache-encryption scheme, hardware attestation, remote rendering, or blockchain. Optional enforcement cannot stop every modified Client or every republished copy, and it does not prevent free or self-published Resources from working.

### Keep purchase evidence private

Disclose only the right needed for the operation, not the buyer's receipt, payment details, or complete purchase history. Proofs do not belong in a public Profile or a relay's shared content package. Even an opaque, stable proof identifier can link a person across Worlds.

Avoid a compulsory issuer callback on every World visit. Online policies need explicit outage behavior and a privacy-aware status mechanism. The [W3C Verifiable Credentials model](https://www.w3.org/TR/vc-data-model-2.0/) is a reference for issuer, holder, verifier, and disclosure boundaries, not a commitment to adopt its entire stack. It explicitly separates cryptographic verification from deciding whether a claim is true or acceptable.

## Lessons from Deployed Services

These are working distribution patterns, not evidence that any particular anti-piracy mechanism succeeds:

- **GOG:** optional launcher and offline installers illustrate deliberate user-controlled preservation, with online functionality treated separately. The design lesson is to preserve a usable build, not just an account entry. [Developer guidance](https://docs.gog.com/introduction/).
- **itch.io:** delivering uploaded files without adding platform DRM shows that payment and file delivery can be separate from execution controls. A store download is not necessarily a complete independent backup. [Creator FAQ](https://itch.io/docs/creators/faq).
- **VRChat Avatar Marketplace:** purchases provide Avatar-menu access rather than source files. Continued access after creator delisting is a platform policy, not a promise of survival after the platform closes. [Buyer FAQ](https://help.vrchat.com/hc/en-us/articles/41810354825235-Avatar-Marketplace-FAQ).
- **Steamworks:** an authenticated backend can verify a user's application ownership. This is a precedent for issuer-backed service checks, not verification by nickname or universal proof of creative authorship. [Authentication and ownership guidance](https://partner.steamgames.com/doc/features/auth).
- **Fab:** its license summary distinguishes source-format access from reference-only access and distinguishes using assets in projects from standalone redistribution. File delivery and allowed uses need separate descriptions; the full applicable terms govern. [License summary](https://www.fab.com/eula).

## What Still Needs Specification

[Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md) tracks offer and Entitlement formats, issuer delegation, holder verification, online checks, extensible claims, offline trust, key recovery, status, transfers, and archive completeness. Initial interoperability tests should cover shared files with detached rights, legitimate holder restoration, failure to demonstrate holder control, wrong-recipient or replayed proofs, unknown required conditions, restoration without the original services, an unavailable issuer, and disallowed account transfer. These tests are planned, not implemented.

## Related Topics

- Overview: [Start Here](../Start%20Here.md)
- Portable content: [Avatars](./Avatars.md) and [Items and Ownership](./Items%20and%20Ownership.md)
- Releases and backups: [Resource Model](./Resource%20Model.md) and [Publishing and Delivery](./Publishing%20and%20Delivery.md)
- Accounts and saved references: [Identity and Profiles](./Identity%20and%20Profiles.md) and [Collections and Sharing](./Collections%20and%20Sharing.md)
- Local policy: [Safety and Moderation](../Trust/Safety%20and%20Moderation.md)
