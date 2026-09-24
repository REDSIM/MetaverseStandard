# Collections and Sharing

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> A person can organise Worlds, Avatars, and Items privately, then choose which parts to share. Saving a Resource does not make it public or prove ownership.

## At a Glance

- A **Collection** is an organised set of saved references, such as favourite Worlds or available Avatars.
- Collections can be local or synchronised through a chosen Account Provider.
- **Audience** describes who may see information. It is separate from a Client Permission to let application code access that information.
- Private is the default for new personal Collections; publishing is an explicit choice.
- Providers can extend the model without making basic Profiles, friends, and Collections provider-specific.

## The Personal Data Model

| Part                  | Meaning                                                                                     | Visibility direction                                                                      |
| --------------------- | ------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| **Account**           | Optional persistent identity used for sign-in, synchronisation, and federation              | Credentials and recovery material are never public profile fields                         |
| **Profile**           | Chosen name, picture, description, and other presentation fields                            | User-selected audience; a session may still receive an approved basic representation      |
| **Friends**           | Relationships and pending requests, separate from arbitrary contact lists                   | Relationship-list visibility is configurable; a relationship is known to its participants |
| **Social messages**   | Private conversations, Join Requests, and Invitations delivered through compatible services | Selected recipients, subject to sender controls; not public Profile fields                |
| **Collections**       | Saved Worlds, Avatars, Items, and optionally other Resource Types                           | Private, friends, selected people, or public                                              |
| **Account Presence**  | Availability, optional status, and selectively shared activity                              | Separate controls for status and World/session location                                   |
| **Personal settings** | Preferences and optional synchronisation settings                                           | Private; device-specific grants are not shared automatically                              |

Every user can have a local Profile. A federated account adds cross-device and cross-provider functions; it is not a requirement to open a local World. [Identity and Profiles](./Identity%20and%20Profiles.md) owns sign-in, identity, and federation.

Collections have an explicit owning Profile context. Using several Profiles under one account does not automatically merge their Collections or publish the connection between them; sharing or reusing data across contexts is deliberate.

## Collections, Not Copies of Everything

A Collection entry points to a Resource and may carry the user's label, note, tags, ordering, or preferred version. Saving a reference does not upload the Resource or require the Account Provider to store its large Content Files.

Favourite Worlds, workspaces, Avatar choices, and Item collections use the same basic concept. A Client can present them as folders, categories, lists, or another accessible interface. Nested folders and specialised views can be added without prescribing one UI or requiring a deeply nested hierarchy.

Collections are not authoritative inventory. An Item reference does not establish quantity, exclusive ownership, or an [Entitlement](./Commerce%20and%20Usage%20Rights.md). An Avatar in a Collection is not automatically the Avatar used in every World; selection remains per World Presence under Client policy.

A saved reference also does not guarantee that the files will remain available. Local file preservation and content-hosting policies are separate from synchronising the list. An unavailable entry remains identifiable rather than silently disappearing.

Exporting a Collection saves references and organisation, not automatically a [Portable Archive — Cache and Portable Archives](./Publishing%20and%20Delivery.md) of each Resource or its purchase evidence. Clients distinguish these export operations and report which content and rights can actually be restored.

## Audience and Sharing

The initial audience choices are **Only me**, **Selected people**, **Friends**, and **Public**. “Friends” refers to an authenticated relationship, not a similar display name or a provider-wide group. Audience checks work across participating providers, not only within one hosting service.

The Collection has a default audience. Entry-specific restrictions can narrow it; putting a private entry inside a public folder does not silently publish it. Sharing a broader audience requires an explicit change to the relevant policy. The Client shows the effective audience before publishing or moving shared content.

Names, descriptions, notes, thumbnails, and the existence of entries are data too. A private collection is not protected if an unauthorised viewer can still enumerate its titles or counts. Personal notes remain private unless explicitly included in sharing.

A shared reference does not broaden access to the referenced Resource. For example, publishing a Collection that mentions a restricted Avatar does not grant everyone access to that Avatar's files. Sharing reference metadata can still disclose its existence, which the Client should make clear.

The data-holding service enforces access when returning data; hiding a UI element is not protection. Application access follows a second boundary: an Overlay App may need a Client Permission to read a Collection even when the user owns it. An app's grant never changes the Collection's public audience.

## What Privacy Can and Cannot Mean

Audience changes affect future access. Previously authorised viewers may retain copies, so making a public Collection private cannot undo all prior disclosure. A friend removal or block stops future access granted through that relationship, subject to the federation's revocation rules; it cannot erase the other person's knowledge.

Private from other users does not automatically mean private from the storage provider. Protecting content from the provider requires an additional end-to-end design, including recovery and sharing; that remains open. Private records are not placed in a publicly replicated repository and merely marked hidden.

World location and join access are separate. Sharing “available” need not reveal a World Address; sharing a World Address does not override that session's admission rules. Multiple [World Presences — Multiple shared presences](./Identity%20and%20Profiles.md) can have different audiences, and a Join Request addresses the person rather than assuming one current World. A private Profile does not make someone invisible to people sharing the same World.

## A Common Baseline, Extensible Services

The proposed account baseline covers Profile fields, friend relationships, social messaging and invitations, Collections of Worlds/Avatars/Items, audience settings, and portable export/import. Implementations claiming this baseline support its meanings; a provider-specific extension is not a substitute for a missing baseline function.

One provider may offer the complete experience or coordinate several compatible services. Authentication, social data, Resource hosting, and commerce remain separate roles. Storage quotas and available service levels may differ, but failures and limits should be visible rather than silently dropping user data.

Extensions use distinct, versioned names and declared data meanings. A provider might add event calendars or Avatar outfit organisation without changing what a standard Collection means. Unknown optional data is not executed, made public, or used to grant access. Migration preserves it where safely supported or reports what cannot be transferred; unknown privacy rules fail closed.

Provider migration aims to preserve identity references, Collections, audiences, and relationships where both sides support the contracts. Account portability alone does not automatically migrate private data, purchases, files, or every extension. Users need a clear export and migration report.

## Current Proposals

Use familiar social and favourites workflows as user-experience input: [VRChat's menus](https://help.vrchat.com/hc/en-us/articles/28526267258515-Getting-Started-with-VRChat) distinguish friends, invitations, recent activity, and favourite or uploaded content. Our proposal adds provider-independent data and privacy rules rather than copying that product's UI.

Borrow extensible record-type ideas from [AT Protocol Lexicons](https://atproto.com/guides/lexicon), without equating an AT Protocol storage collection with a user-facing folder.

Exact audience inheritance, federation revocation, private synchronisation, conflict resolution, migration, and extension handling remain in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md). This note defines the intended model, not its wire schema.

## Related Topics

- Overview: [Start Here](../Start%20Here.md)
- Identity and friends: [Identity and Profiles](./Identity%20and%20Profiles.md)
- Discovery: [Finding Worlds](./Finding%20Worlds.md)
- Shared interfaces: [Platform Services](./Platform%20Services.md)
- Application access: [Features and Permissions](../Client%20Platform/Features%20and%20Permissions.md)
