# Open Metaverse Standard

An open-standard proposal for a **spatial Web** built around places, people, physical interaction, and multiplayer, across desktop, mobile, VR, and AR.

Imagine entering a game, meeting friends, or opening a shared workspace as easily as visiting a website. You choose the Client you use, creators choose how to build and host their experiences, and neither needs to belong to the same company's platform.

This repository documents the **concept and architecture**. It is not yet a finished technical specification, working engine, or production-ready protocol. Proposed technologies and unresolved decisions are marked as such.

## A Web You Can Enter

The modern web is the reference: a shared foundation on which independent applications and services can be built. Here, that model extends into interactive space.

| On the web | In this proposal |
| --- | --- |
| Websites and web applications | **[Worlds](./Concepts/Worlds%20and%20Sessions.md)**: interactive places, games, and applications |
| Web browsers | **[Clients](./Client%20Platform/Client%20and%20Runtime.md)**: user-chosen applications that open Worlds, render them, and protect the device |
| Links between pages and applications | **[World Addresses](./Concepts/Finding%20Worlds.md) and [Portals](./Concepts/Worlds%20and%20Sessions.md)**: ways to discover and move between experiences |

People are more than visitors behind a cursor. The platform is designed around user-controlled identity, Avatars, voice, shared presence, and interaction through hands, controllers, or other supported inputs. Participants could pick up objects, build together, or share a space that extends into their physical surroundings through AR. Desktop and mobile participation remain part of the same vision. A headset is not required for every experience.

Profiles, friendships, and compatible portable content should not be trapped inside one service. Users choose what to carry or disclose across Worlds, while each World retains its own rules and supported interactions.

The vision is not limited to VR, AR, or desktop applications. It is a general-purpose platform intended to support the breadth of modern computing through open, extensible interfaces: spatial and flat interfaces, media, networking, data processing, connected devices, and independently developed backends. AR experiences are one possibility developers can build, not a separate closed platform. There should be no fixed catalogue of what creators are allowed to make. Practical limits come from supported interfaces, hardware, security, and user consent, rather than an artificially narrow set of use cases.

## Open Infrastructure, Not One Company's Platform

The ambition is broader than one social VR application or a collection of spaces inside a proprietary service. It is a shared application platform, closer in scope to the modern web: content formats, execution, identity, communication, discovery, and device access working together through open contracts.

Anyone should be able to implement a Client, publish a World, self-host, or offer compatible services. Identity, content hosting, search, multiplayer services, and stores can have independent providers. No single company should own the network, control its only entry point, or require everyone to use its account system.

The goal is an accessible, distributed ecosystem with an open foundation, not mandatory proprietary infrastructure. Independent creators, communities, and businesses can build on it without becoming the owners of the platform as a whole.

## A Foundation for Future Games and XR Devices

In the long term, developers could target the Metaverse standard when creating new games and experiences, much as developers target the web today. Worlds need not be social hubs: they could be complete single-player or multiplayer games, simulations, creative tools, or everyday applications, with their own logic and backends. The ambition is broad enough for new games of any genre. Actual compatibility will depend on the Features, performance, and interfaces the evolving standard can support.

A Client could remain an ordinary application on a computer or phone, or become the main interface of an XR headset: a high-level environment above an operating system such as Linux, from which users open Worlds and manage their digital lives. These are two ways to deliver the same platform, not a requirement to replace the underlying OS.

This is the long-term direction. The first milestone is smaller and testable: independently built Clients safely opening and running the same World.

## What the Project Covers

- **Portable content:** Worlds, Avatars, Items, and Overlay Apps share a publication and delivery model.
- **Safe Clients:** isolated behavior, explicit permissions, device limits, and predictable fallbacks.
- **Independent services:** identity, content hosting, sessions, relays, and optional commerce or moderation.
- **Cross-platform experiences:** one architecture with optional Features, rather than a requirement that every device support everything.
- **Open interoperability:** reuse modern web standards where they fit and define testable contracts where they do not.

## Start Reading

| If you want to… | Start here |
| --- | --- |
| Understand the idea without implementation details | [Start Here](./Start%20Here.md) |
| See the independent components and where they run | [Architecture Overview](./Architecture%20Overview.md), including the [SVG system map](./Reference/Diagrams/metaverse-architecture.svg) |
| Explore the detailed topics | The reading guide in [Start Here](./Start%20Here.md) |
| Look up a term | [Glossary](./Reference/Glossary.md) |
| See what is settled, proposed, or missing | [Open Decisions and Roadmap](./Reference/Open%20Decisions%20and%20Roadmap.md) |
| Suggest a change | [Contributing](./CONTRIBUTING.md) |

Each topic begins with a short explanation, then goes deeper into behavior, boundaries, failure cases, and open questions. The same Markdown files can be read on GitHub or opened as an Obsidian vault. No publishing build or community plugin is required. See [Contributing](./CONTRIBUTING.md) for editing and navigation conventions.

## Authors and AI Assistance

The original project concept and design direction are by **[REDSIM](https://github.com/REDSIM)** and **[vard88508](https://github.com/vard88508)**. This documentation records and develops their ideas openly.

AI tools assisted with drafting, organization, editing, research, and design review. They are documentation aids, not the credited originators of the project. Existing standards and third-party work remain attributed to their respective creators. We do not claim to have invented the technologies this proposal builds on.

See [Authors and Attribution](./AUTHORS.md) for crediting this work.

## License

The documentation and original diagrams are open content under **[CC BY 4.0](./LICENSE.md)**, to the extent the contributors hold the rights being licensed. You may share and adapt them, including commercially, under its attribution conditions: credit the authors, retain the supplied notices and source link where reasonably practicable, link the license, and indicate changes.

This is the open documentation of a proposed standard, not a software implementation. Ideas and methods themselves are not made exclusive by this license. Independent implementations are welcome. Crediting the project when building on its ideas is appreciated, but is not presented as an additional restriction on otherwise unprotected ideas. See [Authors and Attribution](./AUTHORS.md) for scope and an example credit.
