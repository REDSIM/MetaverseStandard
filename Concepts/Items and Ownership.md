# Items and Ownership

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> An Item is portable content integrated into a World. It can be a fixed sign, an interactive panel, a tool, or an accessory. Being movable, collectible, or owned by a participant is not what makes it an Item.

## At a Glance

- One Item Resource can appear as a fixed AR sign, dynamically loaded panel, world object, held tool, or wearable accessory when the World permits those roles.
- Placement, movement, pickup, attachment, and inventory membership are independent options, not requirements for every Item.
- Worlds choose what to discover, load, arrange, and remove, including content from external feeds. There is no mandatory discovery service or spatial composition algorithm.
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
| **Item Instance** | A running copy with World-defined placement and state, plus a holder or attachments where supported |
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
2. **Placement and attachment**: supported placement, anchor, grip, wearable, or socket metadata.
3. **Self behavior**: sandboxed code controlling only the Item Instance's allowed presentation and local state.
4. **World integration**: a versioned interface understood by a particular World family or game.
5. **Issuer state**: persistent state or Entitlement verified by an authoritative service.

Levels 1 through 3 can be portable through the common Resource Sandbox and behavior API. Complex World effects still require common physics, timing, authority, and game semantics or an explicit World integration contract.

These levels describe interoperability promises, not a mandatory sequence of interactions. A fixed sign does not need pickup or inventory support. A usable tool need not be transferable between people.

## Loading and Composing Items

A World can load Items when it starts or later while it runs. Its developer decides which sources to query, how to interpret a feed, where to place results, and when to replace or unload them. The same approach works for a gallery, a procedural environment, or an AR World showing nearby information. These are application choices, not a required global service or shared spatial composition protocol.

The World may also fetch ordinary application data through [approved networking](../Client%20Platform/Networking.md) and generate scene objects through its own code. Not every generated sign, mesh, or panel needs to be a separately published Item. An imported Item is different: it has its own Release and declared requirements, and any behavior runs in its own sandbox. External data still needs validation and budgets.

Client-managed activation of Resources or code modules always uses the validated loading path. A World can implement its own interpreter for downloaded data, but that work stays within the interpreting World's existing authority and budgets. It does not create a separately trusted Item or obtain new Permission Grants. The Client does not promise to recognize every executable meaning an application may assign to data.

The shared loading path applies whether an Item comes from a participant, a saved reference, or a World-selected source:

1. The World or user selects an Item reference and the World checks its integration policy and budgets. The Client discloses only participant metadata needed for that integration.
2. The Client resolves the exact Release and checks its Manifest, compatibility, and any required rights under the applicable policy.
3. The Client retrieves and validates the selected files and dependencies before activation. Participant-supplied Items use the normal [Resource Relay and cache path](./Resource%20Relay.md), not an arbitrary direct viewer-to-host connection.
4. The World creates an Item Instance with checked integration interfaces and explicitly assigned state authority.
5. The Client starts supported behavior in that Item's Resource Sandbox. Unsupported behavior uses a safe fallback when possible.

Each newly selected Release goes through this path, including replacements introduced by a changing feed. Creating or removing runtime instances does not rewrite the World's immutable Manifest or change a pinned dependency. Changing a published Release or its declared requirements still creates a new Release. Exact dynamic-loading interfaces belong to the shared [Resource lifecycle and composition rules](./Resource%20Model.md).

Equipping an Item never gives it the Avatar’s, World’s, or Overlay App’s Permission Grants.

### Items and Overlay Apps

A panel loaded and managed inside a World can be an Item. A user-controlled tool with an independent lifecycle that may continue across Worlds is an [Overlay App](./Overlay%20Apps.md). Appearance alone does not determine the type. Loading an Item cannot silently install an Overlay App or make it persist outside its hosting World.

## Sandboxed Item Behavior

The default Item host API exposes a Self Handle. Item code may animate its own model, update its own materials and effects, maintain bounded local state, and react to allowed events. It cannot directly set another participant's transform, edit arbitrary World entities, inspect private participant state, or claim authority over shared physics.

External interaction uses typed intents such as `request-activate`, `request-attach`, or a versioned World-specific interface. The World validates the target, distance, permissions, cooldown, and authority before changing shared state. A Client-side Item can predict a result visually, but the authoritative owner decides any valuable outcome.

The Item Manifest lists its code modules, behavior roles, host API versions, budgets, and maximum external Permissions. Network, files, sensors, identity, devices, and persistent external storage remain unavailable until the local user explicitly activates the Item and grants the requested scope. Automatically loaded Items, whether from a participant or a feed, cannot create permission prompts. Valid existing grants may be reused only within their original principal and scope. Loading many Items is not a reason to inherit the World's grants or ask the same question repeatedly.

## State and Authority

Valuable or persistent state belongs under a clearly authenticated authority. A local Client may control cosmetic animation, while an Authoritative World Server or issuer controls competitive effects, inventory, scarcity, or transfer.

Every state field needs an owner, validation rules, replay behavior, and conflict policy. A Message Relay alone does not establish honest state.

## Commerce and Transfers

[Commerce and Usage Rights](./Commerce%20and%20Usage%20Rights.md) defines the shared model for Items and other Resources: independent stores may sell sources, Portable Archives, service access, or combinations. Optional Entitlements remain separate from immutable Item files. Base Item use has no universal purchase-proof requirement. A World may require accepted proof under its stated rules.

Exclusive online inventory and transfer remain issuer- or server-authoritative state. They do not establish that offline files cannot be copied. Backup, account changes, resale, and service-closure behavior follow the particular offer, not the presence of an Item in a Collection.

## Safety and Failure

- Automatically downloaded Item code runs only in a self-scoped Resource Sandbox with strict budgets.
- Invalid or blocked Items use a safe placeholder.
- A failed Item or feed does not normally stop the World. The World may hide that content, keep a validated previous instance, or show a fallback.
- A World may disable Items that obstruct others, imitate System UI, create harmful effects, or provide unfair information.
- Users retain local hide and block controls even when a World accepts an Item.
- Unloading an Item stops its behavior and outputs, invalidates its instance-owned handles, and cancels its pending work. Other World content remains usable. Shared authoritative state and saved data follow their own retention rules.

Unloading is not the same as revoking a remembered Permission Grant. Revocation prevents further approved access but cannot recall data already sent to another service or undo an already accepted shared action. The [Client permission lifecycle](../Client%20Platform/Features%20and%20Permissions.md) applies in both cases.

## Current Proposals

- Treat fixed signs, panels, accessories, and held objects as uses of one Item model, with independent interaction options.
- Standardize appearance, attachments, and self-scoped behavior on one common Resource Sandbox.
- Define safe dynamic activation and unload contracts without prescribing discovery feeds or composition logic.
- Keep Entitlements issuer-neutral and independent of one ledger technology.
- Assign authority per Item state domain.

Placement and attachment vocabulary, dynamic activation and unload interfaces, behavior ABI, issuer federation, transfers, and commerce are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Avatars: [Avatars](./Avatars.md)
- Independent user tools: [Overlay Apps](./Overlay%20Apps.md)
- Sessions: [Worlds and Sessions](./Worlds%20and%20Sessions.md)
- Delivery: [Publishing and Delivery](./Publishing%20and%20Delivery.md)
- Purchases and rights: [Commerce and Usage Rights](./Commerce%20and%20Usage%20Rights.md)
