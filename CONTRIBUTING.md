# Contributing

[Home](./README.md) · [Concept overview](./Start%20Here.md) · [Authors and attribution](./AUTHORS.md)

This is an evolving concept and architecture, not a ratified standard. Useful contributions include clearer explanations, missing use cases, criticism of unsafe assumptions, primary-source research, and small interoperability experiments.

## Proposing a Change

1. Read the relevant topic and the [decision register](./Reference/Open%20Decisions%20and%20Roadmap.md).
2. Explain the user need, affected boundaries, alternatives, and compatibility or security implications.
3. Reuse applicable modern web standards where possible. Cite primary sources and distinguish an adopted direction from a candidate technology.
4. Update the canonical topic, related definitions, and affected links. Do not repeat a rule across several notes when a link is enough.
5. Explain how two independent implementations could test the proposal. Exact schemas and normative requirements come after the concept is clear.

Use an issue or pull request when those repository features are available. For now, concrete discussion and review matter more than a particular contribution workflow. The [governance note](./Reference/Conformance%20and%20Governance.md) tracks the longer-term process.

## One Source for GitHub and Obsidian

Keep a single copy of each document. Use ordinary Markdown with paths relative to the file containing the link:

```markdown
[Resource Model](./Concepts/Resource%20Model.md)
[Home from a topic subfolder](../README.md)
![Architecture map](./Reference/Diagrams/metaverse-architecture.svg)
```

These examples are written from the repository root except for the explicitly labeled subfolder example.

- Include the `.md` extension, use forward slashes, and encode spaces as `%20`.
- Use `./` for links within or below the current folder and `../` for a parent folder. Match filename capitalization exactly. GitHub paths are case-sensitive.
- Link to a topic's actual file, not a vault-specific shortcut, absolute disk path, `obsidian://` URL, or assumed GitHub repository/branch URL.
- Use standard image syntax for SVGs and other illustrations. Keep an adjacent file link when opening a diagram at full size is useful.
- GitHub and Obsidian use different heading-fragment conventions. Shared cross-document references therefore link to the note and name the relevant section in the visible label. They open the document but do not promise automatic scrolling to that section. The reader can use the application's outline to find it.
- Avoid Obsidian-only wikilinks, block references, and note transclusions in published documentation. Do not introduce a required plugin or generated second documentation tree just for navigation.

The compatibility basis is documented in [Obsidian's internal-link help](https://obsidian.md/help/links) and [GitHub's Markdown guide](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Recommended Obsidian Settings

Open this repository folder as a vault, or keep it inside an existing vault. Under **Files and links**, turn off **Use Wikilinks**, choose **Relative path to file** for the new link format, and enable automatic internal-link updates if desired. These settings are recommendations. The documents themselves do not require an `.obsidian` configuration to be distributed.

Local workspace layouts, plugins, and caches stay outside version control. Do not commit private notes, account data, credentials, or machine-specific paths.

## Before Submitting

- Start at `README.md` and check that every changed local link and image resolves, including from nested folders.
- Check both the rendered Markdown and the source. Keep headings readable, tables aligned semantically, and examples clearly non-normative.
- Keep terms consistent with the [Glossary](./Reference/Glossary.md).
- Inspect the file list for local editor state, secrets, temporary files, and unintended changes.
- Do not imply that a proposal has been implemented or passed conformance tests without evidence.

## Attribution and Rights

Submit only material you have the right to share. Documentation and diagram contributions are intended to use the repository's [CC BY 4.0 license](./LICENSE.md), unless a separately reviewed exception is clearly identified. Preserve source attribution and identify third-party material instead of assuming it can be relicensed.

Disclose substantial AI assistance and verify its output. This is a review convention, not a new restriction imposed on downstream users of the license. Software contributions need an explicit software-licensing decision before acceptance.
