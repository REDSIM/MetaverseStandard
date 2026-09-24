# Resource Model

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> A Resource is portable content published in immutable versions called Releases.

## At a Glance

- The initial Resource Types are **World**, **Avatar**, **Item**, and **Overlay App**.
- A **Resource ID** names the continuing project, while a **Release ID** names one exact immutable version.
- A Release is a signed Manifest plus hash-identified content files. It does not have to be one archive.
- One Release may be universal within its declared requirements or contain optional Resource Variants. Separate desktop and mobile builds are not required.
- Visual files, behavior modules, metadata, fallbacks, dependencies, and permission declarations belong to the Release.
- The Manifest is human-readable and available for inspection before activation. [JSON](https://www.rfc-editor.org/rfc/rfc8259.html) is the leading encoding proposal.
- Avatar and Item behavior runs in a nested Resource Sandbox with access to its own runtime instance by default.
- A signed **Publication Record** selects the Resource's current Release. Older Releases remain immutable.

## Core Model

| Object                    | Purpose                                                                                      | Mutable?                                   |
| ------------------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------ |
| **Resource ID**           | Stable identity of a World, Avatar, Item, or Overlay App                                     | No                                         |
| **Application Principal** | Security identity used for storage, Permissions, network policy, and audit                   | Stable, but can enter a new security epoch |
| **Publication Record**    | Signed statement connecting a Resource to its publisher and current Release                   | Yes, through authenticated updates         |
| **Release**               | One complete published version                                                               | No                                         |
| **Release ID**            | Hash of the canonical root Manifest, which in turn names every required Content File by hash | No                                         |
| **Manifest**              | Human-readable structured description interpreted by the Client                              | No                                         |
| **Content File**          | Hash-identified model, texture, audio, behavior module, or other data                        | No                                         |

The **Application Principal** is the equivalent of a web origin: it answers “which application is this?” when the Client stores data or grants access. Its exact construction remains open, but it needs at least a stable Resource ID, verified publisher authority, and a security epoch. A new Release should not lose all storage and grants, while a compromised publisher key must be recoverable without silently inheriting unlimited authority.

A Resource has one current Release. Exact Release IDs may still be used for pinned dependencies, testing, or archived links. A preview World is a separate Resource with its own Resource ID and World Address, not another update path inside the published World.

## What a Manifest Describes

A root Manifest is expected to contain:

- Resource Type, Resource ID, and schema version.
- publisher authority information, bound to the Release through its signed publication.
- entry points, behavior roles, host API versions, and content hashes.
- required and optional Client Profiles, Features, and Limits.
- separately described device-dependent modes, without treating a missing live input device as a file retrieval prohibition.
- declared Permissions and their possible scopes.
- scene or representation data and optional variants.
- pinned dependencies.
- budgets and safe fallbacks.
- optional service interfaces.

Service requirements and network destination declarations belong in the inspected policy description. Mutable service locations may be discovered separately under that policy. Discovery cannot silently broaden access.

The Manifest describes one Release. Discovery metadata, popularity, reviews, host locations, and mutable social information do not belong inside it.

Buyer-specific [Entitlements](./Commerce%20and%20Usage%20Rights.md) also remain separate from the Manifest and Content Files. Issuing a new right does not create a new Release or add a list of buyers to the package. Sales terms and private receipts can refer to the Release without changing it.

The standard does not require visual data, collision data, and behavior state to use separate files or layers. A Resource may organize them together or separately.

## Readable Policy, Separate Consent

The Manifest states what a Resource needs or may request. It does not contain granted access, account tokens, private device identifiers, or a user-editable authorization switch. Permission Grants belong to the Client and its [permission policy](../Client%20Platform/Features%20and%20Permissions.md).

The Client exposes both the verified text and a plain-language summary generated from registered fields. Publisher-written purpose text is a claim, not trusted permission UI. Unknown required security semantics cause rejection rather than being hidden or silently ignored.

Changing requirements creates a new Release, not an in-place change to the verified Manifest. [Runtime requests and update consent: Changes During Use and Updates](../Client%20Platform/Features%20and%20Permissions.md) define how access can change without breaking that identity.

## Current Proposals: JSON Manifest

Use strict UTF-8 [JSON](https://www.rfc-editor.org/rfc/rfc8259.html), with a versioned schema and readable formatting for inspection. High-rate pose packets can use a different compact encoding. There is no reason to force their representation onto an infrequently read Manifest.

For independent verification, evaluate [JSON Canonicalization Scheme](https://www.rfc-editor.org/rfc/rfc8785.html) for the hashed root representation. This needs a constrained JSON profile: reject duplicate keys and invalid encodings, specify number handling, and bound depth, strings, and arrays. Identifiers and hashes are strings, not large JSON numbers. Canonicalization is a defined algorithm, not simply removing whitespace.

Hash the validated canonical root to obtain the Release ID, then bind that ID to publisher authority using a detached signature or signed publication envelope. Do not put a self-dependent Release hash or a signature over the entire root inside that same hashed root. Exact signature layout, algorithms, and key recovery remain open. Content Files are verified by their declared byte hashes.

Illustrative permission fragment only: field names and Permission names below are provisional, and files, hashes, entry points, and other required fields are omitted. It is not a publishable Manifest or a ratified schema.

```json
{
  "resourceType": "world",
  "permissions": [
    {
      "name": "camera.frames",
      "scope": "user-selected-device",
      "purpose": "Take a photo when the user activates the booth",
      "optional": true,
      "fallback": "Keep the World available without photo capture"
    }
  ]
}
```

This declaration grants nothing. The Client still checks device support, chooses the device with the user, and controls scope and duration. A modified policy requires a verified update. Merely editing this JSON does not authorize the running World.

## One Release, Optional Variants

A **Resource Variant** is a declared alternative within the same Release, such as a lighter scene, another texture encoding, or an enhanced graphics path. The model applies equally to Worlds, Avatars, Items, and Overlay Apps.

- Common Content Files are shared rather than duplicated for every variant.
- Each variant identifies its requirements, files, pinned dependencies, and fallback relationship.
- The root Manifest binds all alternatives by hash. Selecting a variant does not change the Release ID.
- A Client retrieves common requirements and the selected path, not every alternative. An offline archive may include several or all variants.
- Rebuilding a published variant or adding one changes the Manifest and therefore creates a new Release. Local compiled caches do not change the Release.

Creators choose whether to provide one portable path or several alternatives. The standard defines how requirements and alternatives are described, not an operating-system build matrix or a particular build tool.

Compatibility is evaluated by [the Client before preparing content: Feature Negotiation](../Client%20Platform/Features%20and%20Permissions.md). Quality preferences are local policy. Unknown formats and missing required execution Features are compatibility failures, not quality choices. A Release can execute only across Clients satisfying at least one complete declared path. Live device availability and approval are checked separately when a mode uses them. Files can be retained without claiming they can run locally.

## Local Lifecycle

The Client may move a Release through these internal states:

1. **Known**: the Client has a reference but no verified Manifest.
2. **Resolved**: the exact Release and publication authority are known.
3. **Selected**: Manifest preflight identifies compatible content and fallbacks within local budgets.
4. **Available**: files needed for the selected path are downloaded and verified.
5. **Validated**: selected content, dependencies, and actual resource use pass checks.
6. **Installed**: optional persistent local preparation is complete.
7. **Active**: a sandboxed runtime copy is running.
8. **Blocked**: policy or validation prevents use.

These names describe observable outcomes for preparing and running content, not a required storage implementation. A retained archive may contain files for currently unsupported paths. Their presence does not imply that they passed preparation or that a required device is currently available.

## Composition Rules

A Resource may refer to other Releases, but every dependency is independently identified and validated. Permissions do not flow through composition: a World embedding an Item does not give that Item the World’s grants, and equipping an Avatar accessory does not give it Avatar privileges.

A World may also select Items dynamically through its supported loading interface. A discovery result is resolved to an exact, verified Release before activation and does not rewrite a pinned dependency or the World's Manifest. Runtime feeds and placement policy belong to the World. [Items and Ownership: Loading and Composing Items](./Items%20and%20Ownership.md) distinguishes separately published Items from ordinary data used to generate scene objects. Client-managed Resource and code-module activation requires validation. Application code interpreting its own input remains under that application's existing authority and budgets, without creating a new Resource identity or additional grants.

Avatars and Items may contain **Resource Behavior**. When the World activates it, the Client creates a **Resource Sandbox** nested inside the World Runtime. The code receives a Self Handle for its own runtime instance and only the host interfaces declared for that behavior profile. It may update its own allowed presentation and local state, but it cannot enumerate or mutate arbitrary World entities.

Interaction outside the Resource Sandbox uses a typed request to a World integration interface. The World validates the request and remains responsible for shared authority. Sensitive external APIs require the Resource's own Manifest declaration and Permission Grant. Automatically received behavior may not create permission prompts merely because another participant entered the World.

## Failure and Fallback

- A missing or invalid file required by the selected path causes a declared fallback or blocks activation if no complete valid path remains. Files exclusive to an unselected variant are not required for activation.
- A missing optional file uses its declared fallback.
- An unsupported required decoding or execution Feature rules out the affected path. If none remain, execution is refused with a clear explanation. Device-dependent modes separately report unavailable or denied access without imposing a blanket download ban.
- An unsupported optional Feature selects a compatible variant.
- An invalid participant Avatar or Item becomes a safe placeholder.
- Failure of Resource Behavior disables that behavior and keeps a declarative visual fallback when possible.
- A publisher signature proves origin, not safety.

## Current Proposals

- Use cryptographic content hashes for Release IDs and content files.
- Encode Manifests canonically so independent implementations sign and hash the same bytes.
- Let hosts and mirrors be discovered separately from the signed Release because hashes protect integrity.
- Use [glTF/GLB](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html) for delivered 3D scenes while defining the application/component contracts in [Worlds and Sessions](./Worlds%20and%20Sessions.md).

Exact addressing, canonical encoding, signatures, dependency rules, and principal derivation are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Delivery: [Publishing and Delivery](./Publishing%20and%20Delivery.md)
- Runtime: [Client and Runtime](../Client%20Platform/Client%20and%20Runtime.md)
- Compatibility: [Features and Permissions](../Client%20Platform/Features%20and%20Permissions.md)
- Terms: [Glossary](../Reference/Glossary.md)
