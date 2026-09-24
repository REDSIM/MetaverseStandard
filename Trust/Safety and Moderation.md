# Safety and Moderation

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> Security protects systems and data; safety controls help people leave, filter, and recover from harmful experiences and other users.

## At a Glance

- Local mute, hide, block, personal-space, comfort, and emergency-exit controls always remain available.
- Worlds can add rules and moderators, but they cannot remove Client-level protection.
- Content labels and moderation services are federated: users and communities may choose providers and policies.
- Physical, sensory, social, and privacy harm are separate categories with different mitigations.
- Reporting reveals only the evidence the user deliberately submits.

## Local Safety Controls

The Client provides controls that work across Worlds:

- mute voice or other audio by participant or application;
- hide or replace Avatars, Items, effects, and Overlay Apps;
- block a participant across selected Profiles or contexts;
- enforce personal-space and contact boundaries;
- reduce flashes, motion, volume, haptics, particles, and visual complexity;
- stop camera, microphone, tracking, and spatial sharing;
- show which World and Profile are active;
- leave the foreground World immediately through trusted System UI.

These controls do not depend on a World script continuing to run.

## Physical and Sensory Safety

Immersive content can cause harm without exploiting software. Clients therefore need user-controlled limits for:

- locomotion and camera motion;
- flashing and high-contrast patterns;
- sudden or sustained audio levels;
- haptic intensity and duration;
- content near the face or inside personal space;
- passthrough, guardian, and physical-boundary visibility;
- interaction while seated, standing, or moving;
- accessibility alternatives for required gestures or senses.

A World declares relevant comfort and hazard metadata, but the Client may enforce stricter policy. User safety bounds are not presentation suggestions.

## Moderation Layers

| Layer | Examples | Authority |
| --- | --- | --- |
| **Local** | mute, hide, block, replacement, filters | User and Client |
| **World** | rules, kick, ban, role permissions, instance moderation | World operator |
| **Community** | shared block lists, labels, reputation, moderation feeds | Chosen community provider |
| **Service** | account, hosting, relay, or marketplace enforcement | Service operator |
| **Legal** | required reporting or removal | Applicable jurisdiction |

No one moderation provider needs to control the whole ecosystem. Users and communities can subscribe to several services, inspect their policies, and keep local overrides where law and platform safety allow.

## Identity and Blocking

A visible name or Avatar is not a reliable identity. Blocking therefore needs to consider the best authenticated subject available while avoiding unnecessary disclosure of a global Account ID.

Pairwise Participant IDs complicate cross-World blocking by design. A future protocol may allow a user to create a privacy-preserving block token or ask their Account Provider to enforce a block without revealing both parties’ full account identity to every World.

## Content Labels

Labels may describe age suitability, flashing, motion intensity, violence, sexual content, gambling, user-generated content, voice recording, location use, or other risks. Labels are signed claims from a Publisher or moderation provider, not universal facts.

Clients show label source and apply user-selected policy. Missing labels are treated as unknown rather than safe.

Optional purchase or usage verification follows [Commerce and Usage Rights](../Concepts/Commerce%20and%20Usage%20Rights.md). A community can require rights from accepted issuers without imposing that policy on the whole network. Failure to establish eligibility is not itself a finding of theft; accusations and disputes require a separate evidence and appeal process.

## Reporting

A report can include selected identifiers, timestamps, messages, media, or local logs. The Client previews exactly what will be sent, to whom, and under which Profile. Continuous recording is never assumed merely to make later reporting easier.

Services publish receipt, appeal, retention, and transparency behavior where appropriate. Evidence authenticity and privacy must be balanced; the standard cannot promise that every dispute has complete evidence.

## Multi-World Safety

- Only the foreground World controls immersive attention.
- Background audio and notifications are independently adjustable.
- A background World cannot open a safety prompt over trusted System UI.
- Emergency exit stops or suspends risky output from all Resources, not only the foreground World.
- The Client can show which sessions still represent the user as Connected or Away.

## Current Proposals

- Standardize the minimum local control vocabulary before federating moderation data.
- Define signed, source-visible labels and user-selected moderation feeds.
- Keep safety controls in the Client Core path, independent of World behavior.
- Treat privacy-preserving cross-provider blocking as an open research problem.

Block semantics, label registry, evidence format, child safety, moderation federation, and appeals are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Threat model: [Security and Privacy](./Security%20and%20Privacy.md)
- Identity: [Identity and Profiles](../Concepts/Identity%20and%20Profiles.md)
- Runtime controls: [Client and Runtime](../Client%20Platform/Client%20and%20Runtime.md)
