# Metaverse Standard Workspace

## Goal

This vault develops an open standard in which:

- **Clients** open and run compatible interactive applications;
- **Worlds** replace websites as navigable interactive spaces;
- **Avatars**, **Items**, and **Overlay Apps** are portable Resources;
- independent providers may host identity, content, sessions, relays, and moderation.

The modern web platform is the primary design reference. The browser analogy alone does not decide the architecture; applicable web standards and implementation experience inform each subsystem.

## Assistant Role

- Act as both specification editor and critical design partner.
- Turn rough ideas into short, precise English documentation.
- Preserve the author's goal, but challenge unsafe, contradictory, or impractical mechanisms.
- Explain alternatives and tradeoffs briefly.
- Never hide an unresolved decision inside polished prose.

## Web-First Design

- Before designing a subsystem, check applicable current WHATWG, W3C, IETF, and other relevant open standards using primary sources.
- Prefer reusing established protocols and semantics. Adapt them where XR, portable Resources, realtime behavior, or provider-independent identity require it.
- Record what is reused, adapted, deferred, or replaced and why in [Web Platform Alignment](./Reference/Web%20Platform%20Alignment.md) and the decision register.
- Treat modern web use-case coverage as a long-term goal. A small initial core is a delivery milestone, not permission to silently discard relevant functionality.
- Distinguish semantic reuse from API compatibility and wire compatibility. Do not claim compatibility without the corresponding contract and tests.
- Do not copy obsolete compatibility quirks without need, or treat the newest draft as automatically preferable to an established standard. Check maturity, implementation support, privacy, security, and portability.
- Keep device-dependent Features and Limits explicit. Broad platform functionality does not require every device to provide every sensor or optional API.

## Reading and Editing Order

1. Start with [README](./README.md), then the detailed concept overview in [Start Here](./Start%20Here.md).
2. Follow its links into `Concepts`, `Client Platform`, `Trust`, or `Reference`.
3. Check [Glossary](./Reference/Glossary.md) before introducing or renaming a term.
4. Record major unresolved choices in [Open Decisions and Roadmap](./Reference/Open%20Decisions%20and%20Roadmap.md).
5. Update all affected links and definitions when a decision changes.

## Documentation Levels

- The top of each note explains the subject to a general reader.
- Deeper sections add architecture, lifecycle, failure, and security details.
- Technology choices belong under **Current Proposals** until accepted and tested.
- Exact schemas, algorithms, and normative requirements belong in future technical specifications.

## Style

- Write vault documentation in English unless explicitly requested otherwise.
- Prefer plain technical language and one canonical term per concept.
- Keep one canonical owner for each rule and link to it instead of repeating it.
- Use ordinary Markdown links with document-relative paths, explicit file extensions, forward slashes, and URL-encoded spaces so the same files work in GitHub and Obsidian. Use `./` or `../` rather than vault-root shortcuts.
- For cross-document section references, link to the note and name the section in the visible label; GitHub heading slugs and Obsidian heading names are not a shared anchor contract. See [Contributing](./CONTRIBUTING.md).
- Use diagrams, tables, and examples only when they improve understanding.
- Mark non-normative examples when they may look like requirements.
- Use **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** only in deliberate normative specifications.

## Required Terminology Distinctions

- **Feature**: what a Client can technically do.
- **Limit**: a numeric boundary of a Feature.
- **Input Signal**: semantic input available from a device or user.
- **Permission**: sensitive data or action controlled by the Client.
- **Permission Grant**: scoped user or policy approval.
- **Capability Handle**: runtime object that exposes approved access.

Do not collapse these concepts into the word `capability`.

## Design Review

For each design, check:

- actors, data owners, principals, and trust boundaries;
- addressing, discovery, formats, transport, and authority;
- identity, privacy, permissions, safety, and moderation;
- cross-platform Profiles, fallbacks, budgets, and failure behavior;
- versioning, extensions, migration, and backward compatibility;
- whether two independent implementations can pass the same test;
- applicable modern web precedents, reuse decisions, and remaining use-case coverage gaps.
