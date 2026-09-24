# Finding Worlds

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> Open a World by its address, find it through a chosen directory, or return through your saved Collections. No single global catalogue is required.

## At a Glance

- The Client offers navigation without requiring a particular home World or an account provider.
- A World has a shareable address, but does not need its own domain.
- **World Directories** are replaceable search and catalogue services, not authorities over World identity.
- Search can describe public places and activities inside a World, not only its title.
- Search visibility, permission to enter, and permission to inspect private data are different controls.
- A Portal is an optional spatial way to follow a navigation target; the same action can be offered in Client UI.

## When the Client Opens

A recommended starting experience is a Client-controlled panel with an address/search field, saved Worlds, recent visits, a Profile selector, and optional friend activity. This is a usability recommendation, not a required layout. It works in desktop, mobile, or XR presentation.

The user can:

- open a pasted address, invitation, or local World;
- choose a saved World from [Collections](./Collections%20and%20Sharing.md);
- search a selected World Directory;
- open a chosen home World or resume an earlier session.

A home World is optional content, not the only way to navigate. A 3D search World is also possible, but does not replace trusted address, account, or safety controls. A broken or unavailable home World leaves those controls usable.

Recent visits stay local by default. Resuming content does not silently restart microphone capture, publish location, or join every previously open online session; existing lifecycle and consent rules still apply.

## Address, Identity, and Hosting

| Concept | What it answers |
| --- | --- |
| **World Address** | How can the Client resolve and open this World? |
| **Resource ID** | Which continuing World is this, across updates? |
| **Release ID** | Which exact published version is being opened? |
| **Host or Mirror** | Where can the verified files be obtained now? |

A domain-based address is a useful candidate. One domain can serve many Worlds through different paths, and a creator can use an address supplied by a hosting service instead of buying a domain. A domain is not the World itself and need not be the Account Provider or search provider.

Address resolution leads into [standard publication retrieval](./Platform%20Services.md). Search results are not required for that flow. Domain changes and redirects do not silently replace a previously verified publisher identity; migration and recovery need explicit rules.

An address may also identify a public location or session within a World. Such a link requests navigation, not permission to enter a restricted session or execute privileged commands. Exact address syntax remains open.

## Replaceable Directories

The Client can have a default directory and support user-selected alternatives through a shared search interface. A directory may be a search engine, curated catalogue, community service, or an organisation's private index. None has to contain every World.

Search results provide portable World Addresses and useful card information: title, description, preview, publisher identity, language, access conditions, and relevant compatibility information. The Client renders these through its own UI. Ranking, recommendation algorithms, and presentation remain implementation choices; commercial or inferred claims remain distinguishable from publisher information.

Queries go to the selected provider, not automatically to every known directory. Search providers can observe queries and connection metadata. Private Collections, visit history, friend lists, and identity are not automatically sent for personalisation.

Previews are bounded, untrusted media, not code from the World. Loading a results list should not contact every listed World's host or expose the viewer to arbitrary preview trackers; directories can serve cached previews through approved delivery paths.

## What Can Be Indexed?

| Layer | Searchable information | Direction |
| --- | --- | --- |
| **World listing** | Title, summary, tags, language, preview, publisher, and public entry address | Baseline discovery metadata |
| **Publisher-provided content description** | Public places, objects, activities, exhibits, text, and optional links to locations inside the World | Optional way to improve discovery without running the World |
| **Provider-derived index** | Text or visual features extracted from permitted public content or a published snapshot | Optional directory implementation; inferences may be wrong |

For example, a museum World may publish descriptions of its exhibits. Searching for a telescope can then find its astronomy room even if the World title never mentions telescopes.

The standard defines how discovery information is published and interpreted, not one mandatory crawler, AI model, or ranking algorithm. A creator does not have to annotate every object. Search coverage can be incomplete, especially for procedural or server-generated experiences.

Dynamic Worlds may publish an updated public discovery feed or summary. That does not require rewriting the immutable Release merely because an exhibition or event changes. Results identify their source and freshness; publisher statements and search-engine inferences are not guarantees of current content.

Baseline indexing does not execute World behavior. A provider choosing deeper rendering or inspection treats it as untrusted work under its own isolation and budgets, without visitor credentials. Running code is neither required nor sufficient to discover every possible state of a World.

## Discovery and Privacy

- Public listing is separate from access to the World or its sessions. A listed World can require sign-in; an unlisted address is not a secret or an access-control mechanism.
- Publishers can express indexing preferences. Cooperative directories honor them, but a preference cannot prevent copying information already made public.
- Private sessions, live participant conversations, visitor Avatars, personal files, and transient user activity are not included in ordinary World indexing.
- Restricted organisation searches need access-checked results and previews; titles and snippets can themselves reveal private information.
- Removal or a visibility change stops future authorised exposure, but cannot guarantee deletion of copies already received by another party.

The web's [Robots Exclusion Protocol](https://www.rfc-editor.org/rfc/rfc9309.html) explicitly separates crawler preferences from access authorization. That distinction also applies here; the exact World indexing policy is still a proposal.

## Portals and Navigation

A **Portal** is a spatial marker for opening a World or joining a particular session. It is another presentation of navigation, not a separate Resource Type, transport, or way to bypass access controls. Clients share its meaning and activation contract, not necessarily its appearance. A link or Client UI action provides a fallback when spatial portals are unsupported or unwanted.

A World may include authored portals or allow participants to place them. Placement, visibility, position, lifetime, and shared-state synchronisation follow that World's rules. A locally displayed marker does not automatically become a shared object.

The Client owns the transition. It can supply a standard portal marker and trusted destination information; a World may offer custom decoration through supported interfaces. World-controlled appearance is not proof of the target. Authorising a portal's placement never authorises it to move other participants or grant Permissions.

### Deliberate activation

The Client provides a way to inspect the actual World and session before travelling. Activation refers to that inspected target; changing it does not silently reuse approval for the old destination. A different session of the same World is also a different destination.

Clicking, selecting, or an explicitly enabled walk-through interaction can express intent. A portal appearing under the user, moving onto them, or a World moving their Avatar into it is not sufficient permission to switch Worlds. Confirmation style is Client policy, not necessarily a dialog on every use. Merely seeing a portal does not start the destination's code or grant its access requests.

### Shared portals and private invitations

A shared Portal exposes only destination information that may be shared with its viewers. It does not broadcast the creator's private Invitation, account credentials, or recipient-specific admission proof. Even the name of a private session may be restricted.

Each participant enters under their own admission decision. A private Invitation can instead be presented as a private Client-owned Portal or UI action, without turning it into a public ticket. Invitation semantics remain in [Identity and Profiles — Join Requests and Invitations](./Identity%20and%20Profiles.md).

### Transition and failure

The Client can replace the current view, switch to an already open destination, or open another World when supported. Portals do not require multi-World execution. The user can choose to leave the previous session or keep it open where Client limits and session policy allow; a background connection is not guaranteed indefinitely.

Normal publication validation, compatibility checks, admission, and Permissions still apply. A transition does not automatically copy microphone routes, tracking streams, private World state, or grants to the destination. If entry fails or an Invitation is no longer valid, report the failure without silently choosing a different session. Local navigation and recovery remain available.

The web's [sandboxed navigation model](https://html.spec.whatwg.org/multipage/browsers.html#sandboxing) provides a reference for separating content requests from user-agent authority. XR needs its own safe activation and comfort rules; this is semantic guidance, not an adopted browser API.

## Current Proposals

Start with direct addresses, local saved Worlds, and a shared directory search contract. Add optional publisher-provided content descriptions before requiring expensive scene inspection.

These are conceptual proposals. Address syntax, discovery records, crawl policies, freshness, and directory federation remain in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Overview: [Start Here](../Start%20Here.md)
- Client responsibilities: [Client and Runtime](../Client%20Platform/Client%20and%20Runtime.md)
- Shared interfaces: [Platform Services](./Platform%20Services.md)
- Publication and retrieval: [Publishing and Delivery](./Publishing%20and%20Delivery.md)
- Saved content: [Collections and Sharing](./Collections%20and%20Sharing.md)
