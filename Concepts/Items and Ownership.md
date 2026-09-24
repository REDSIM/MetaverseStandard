# Items and Ownership

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> An Item is portable content that a World may display, place, hold, or attach. Portability does not force every World to support the same behavior.

## At a Glance

- One Item Resource can appear as a world object, held object, wearable accessory, or device when the World permits those roles.
- The published Item, a running Item Instance, and the right to use it are separate concepts.
- Items may include sandboxed code that controls their own presentation and submits typed interaction requests.
- Complex effects on World state require a shared interaction model or a World-specific integration contract.
- A World may reject, replace, or restrict an Item for safety, fairness, performance, or theme.
- An optional Entitlement records rights granted by an issuer. Its acceptance depends on the verifier's policy, not merely on possession of a content file.

## Core Model

| Object | Meaning |
| --- | --- |
| **Item Resource** | The continuing portable object and its Releases |
| **Item Release** | One immutable appearance and behavior description |
| **Item Instance** | A running copy with a position, holder, attachments, and session state |
| **Entitlement** | Issuer-signed statement granting defined rights under stated conditions. See [Commerce and Usage Rights](./Commerce%20and%20Usage%20Rights.md) |
| **World Integration** | Contract that explains how a World interprets an Item’s optional behavior or state |

An Entitlement is distinct from:

- legal ownership under a jurisdiction.
- possession of an encrypted or public content file.
- control of one runtime Item Instance.
- permission to use the Item in every World.

## Portability Levels

Item interoperability should be promised in explicit levels:

1. **Appearance**: model, materials, bounds, thumbnail, and fallbacks.
2. **Attachment**: standardized grip, wearable, socket, and placement metadata.
3. **Self behavior**: sandboxed code controlling only the Item Instance's allowed presentation and local state.
4. **World integration**: a versioned interface understood by a particular World family or game.
5. **Issuer state**: persistent state or Entitlement verified by an authoritative service.

Levels 1 through 3 can be portable through the common Resource Sandbox and behavior API. Complex World effects still require common physics, timing, authority, and game semantics or an explicit World integration contract.

## Entering a World with an Item

1. The user selects an Item or a Profile references an equipped accessory.
2. The Client discloses only the Item identity and metadata needed by the World.
3. The World checks supported portability level, policy, budgets, and any required Entitlement.
4. Participant Clients retrieve and validate the Release through normal privacy relays or caches.
5. The World creates an Item Instance and the Client starts its behavior in a Resource Sandbox.
6. The authority owner assigns allowed state writers and World integration interfaces.
7. Unsupported behavior is removed or replaced without losing the basic presentation when possible.

Equipping an Item never gives it the Avatar’s, World’s, or Overlay App’s Permission Grants.

## Sandboxed Item Behavior

The default Item host API exposes a Self Handle. Item code may animate its own model, update its own materials and effects, maintain bounded local state, and react to allowed events. It cannot directly set another participant's transform, edit arbitrary World entities, inspect private participant state, or claim authority over shared physics.

External interaction uses typed intents such as `request-activate`, `request-attach`, or a versioned World-specific interface. The World validates the target, distance, permissions, cooldown, and authority before changing shared state. A Client-side Item can predict a result visually, but the authoritative owner decides any valuable outcome.

The Item Manifest lists its code modules, behavior roles, host API versions, budgets, and maximum external Permissions. Network, files, sensors, identity, devices, and persistent external storage remain unavailable until the local user explicitly activates the Item and grants the requested scope. A remote participant's Item cannot prompt the viewer.

## State and Authority

Valuable or persistent state belongs under a clearly authenticated authority. A local Client may control cosmetic animation, while an Authoritative World Server or issuer controls competitive effects, inventory, scarcity, or transfer.

Every state field needs an owner, validation rules, replay behavior, and conflict policy. A Message Relay alone does not establish honest state.

## Commerce and Transfers

[Commerce and Usage Rights](./Commerce%20and%20Usage%20Rights.md) defines the shared model for Items and other Resources: independent stores may sell sources, Portable Archives, service access, or combinations. Optional Entitlements remain separate from immutable Item files. Base Item use has no universal purchase-proof requirement. A World may require accepted proof under its stated rules.

Exclusive online inventory and transfer remain issuer- or server-authoritative state. They do not establish that offline files cannot be copied. Backup, account changes, resale, and service-closure behavior follow the particular offer, not the presence of an Item in a Collection.

## Safety and Failure

- Automatically downloaded Item code runs only in a self-scoped Resource Sandbox with strict budgets.
- Invalid or blocked Items use a safe placeholder.
- A World may disable Items that obstruct others, imitate System UI, create harmful effects, or provide unfair information.
- Users retain local hide and block controls even when a World accepts an Item.

## Current Proposals

- Treat accessories and held objects as modes of one Item model.
- Standardize appearance, attachments, and self-scoped behavior on one common Resource Sandbox.
- Keep Entitlements issuer-neutral and independent of one ledger technology.
- Assign authority per Item state domain.

Attachment vocabulary, behavior ABI, issuer federation, transfers, and commerce are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Avatars: [Avatars](./Avatars.md)
- Sessions: [Worlds and Sessions](./Worlds%20and%20Sessions.md)
- Delivery: [Publishing and Delivery](./Publishing%20and%20Delivery.md)
- Purchases and rights: [Commerce and Usage Rights](./Commerce%20and%20Usage%20Rights.md)
