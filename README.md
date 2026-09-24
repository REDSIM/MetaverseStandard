# Open Metaverse Standard

An open, provider-independent platform for interactive Worlds, portable Avatars and Items, and compatible Clients across desktop, mobile, VR, and AR.

The idea is to make interactive spaces work more like the web: anyone can build a Client, publish a World, or operate compatible services. Users should not need one company's account, hosting, store, or runtime to participate.

This repository documents the **concept and architecture**. It is not yet a finished technical specification, working engine, or production-ready protocol. Proposed technologies and unresolved decisions are marked as such.

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

Each topic begins with a short explanation, then goes deeper into behavior, boundaries, failure cases, and open questions. The same Markdown files can be read on GitHub or opened as an Obsidian vault; no publishing build or community plugin is required. See [Contributing](./CONTRIBUTING.md) for editing and navigation conventions.

## Authors and AI Assistance

The original project concept and design direction are by **[REDSIM](https://github.com/REDSIM)** and **[vard88508](https://github.com/vard88508)**. This documentation records and develops their ideas openly.

AI tools assisted with drafting, organization, editing, research, and design review. They are documentation aids, not the credited originators of the project. Existing standards and third-party work remain attributed to their respective creators; we do not claim to have invented the technologies this proposal builds on.

See [Authors and Attribution](./AUTHORS.md) for crediting this work.

## License

The documentation and original diagrams are open content under **[CC BY 4.0](./LICENSE.md)**, to the extent the contributors hold the rights being licensed. You may share and adapt them, including commercially, under its attribution conditions: credit the authors, retain the supplied notices and source link where reasonably practicable, link the license, and indicate changes.

This is the open documentation of a proposed standard, not a software implementation. Ideas and methods themselves are not made exclusive by this license; independent implementations are welcome. Crediting the project when building on its ideas is appreciated, but is not presented as an additional restriction on otherwise unprotected ideas. See [Authors and Attribution](./AUTHORS.md) for scope and an example credit.
