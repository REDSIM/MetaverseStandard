# Avatars

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> An Avatar is a portable, mostly declarative representation of a participant, not a species-specific character class.

## At a Glance

- Every Avatar supports a small **Avatar Core** that any compatible Client can present safely.
- A **Humanoid Rig** adds standardized body, face, hand, and tracking semantics.
- Unusual bodies remain valid; they may add future semantic rig extensions without creating a type for every anatomy.
- An Avatar may include sandboxed code for animation, effects, and World interaction.
- Avatar code receives access to its own Avatar Instance by default, never arbitrary participants.
- Each World Presence reports the Avatar it uses; the Client decides how that Avatar is selected.
- Worlds and users may replace expensive or unsafe Avatars with fallbacks.

## Avatar Selection

When joining a World or session, the Client communicates which Avatar Release represents the user in that World Presence. The standard does not require a per-World preset or any particular selection interface.

- A Client may reuse one Avatar across all Worlds or use different Avatars in different Worlds at the same time.
- The Client decides whether an Avatar change affects one World Presence, several Worlds, or a default selection.
- Remembering a preferred Avatar for a Profile, World, or device is an optional Client feature.
- A World may restrict Avatars or select a fallback under its published rules, but the Client should show the user what others will see.
- Account Providers, directories, and unrelated Worlds do not automatically receive the user's active Avatar. Sharing that information outside the current World is a separate optional presence feature.

## Avatar Core

The Avatar Core describes the minimum information needed even when the Client does not understand the anatomy:

- root transform, physical scale, and bounds;
- neutral pose and visual scene entry point;
- viewpoint and first-person visibility rules;
- nameplate and voice-source anchors;
- personal-space and basic interaction bounds;
- thumbnail and safe placeholder;
- presentation variants and levels of detail;
- declared performance budgets.

This is more useful than a “generic” label: it defines what all Clients can rely on without claiming to understand the skeleton.

## Rig Profiles

### Humanoid Rig

The first optional rig profile maps well-known human-like bones, eye direction, facial expressions, hands, and common tracking targets. It covers most human, anthropomorphic, and stylized Avatars without requiring identical proportions.

[VRM 1.0](https://vrm.dev/en/vrm1/) is a strong input to this work because it already defines portable humanoid models, first-person behavior, expressions, and gaze. The project should first test whether a compatible subset or extension is enough before inventing another full humanoid schema.

### Semantic Rig

A future Semantic Rig may describe arbitrary articulated anatomy through roles and relationships rather than species names. A centaur, quadruped, vehicle, or multi-limbed creature could expose locomotion roots, grasping limbs, gaze sources, contact surfaces, and other semantic targets.

This remains open research. The standard should not create separate “cat,” “centaur,” or “octopus” types.

## Tracking and Animation

The Client maps available [Input Signals](../Client%20Platform/Devices%20and%20Input.md) to rig targets:

```text
head pose + hand pose + optional trackers
                    ↓
             Client retargeting
                    ↓
       Humanoid or Semantic Rig targets
                    ↓
          final Avatar presentation
```

Missing input is reconstructed or animated by the Client. For example, a desktop user may provide only view direction and movement, while an XR user provides head, hands, eyes, face, and full-body trackers.

The Client owns the participant's semantic pose and tracking pipeline. Avatar code may add visual animation to allowed rig targets, but it cannot change the participant's authoritative World position, collision state, or another participant's pose.

## Avatar Behavior

An Avatar Release may include Resource Behavior for:

- procedural animation and secondary motion;
- expressions, material changes, particles, and bounded audio;
- reactions to the Avatar's own state or allowed contacts;
- typed interaction requests understood by the current World;
- optional features that use explicitly granted Input Signals or external APIs.

The code runs in a Resource Sandbox with a Self Handle for that Avatar Instance. It cannot search for arbitrary entities or call unrestricted World mutation APIs. To interact with a pickup, participant, or World system, it sends a typed intent; the World checks range, policy, state authority, and rate limits before applying any effect.

The Avatar Manifest lists behavior modules, host API versions, budgets, and the maximum external Permissions they may request. A remote participant's Avatar may run self-scoped presentation behavior, but it cannot create permission prompts on the viewer's Client. Sensitive access is available only after an explicit local activation and Permission Grant.

## Cross-Platform Presentation

- Clients select visual quality according to Features, Limits, user settings, and World budgets.
- Worlds may impose stricter Avatar budgets or visibility rules and must explain replacements.
- First-person and third-person visibility may differ to prevent clipping or obstruction.
- A failed or unsupported Avatar becomes a recognizable placeholder while identity and voice controls continue to work.

## Safety and Privacy

The Client keeps local controls to hide, mute, block, reduce effects, enforce personal space, and replace an Avatar. Avatar files and their origin may reveal user interests or identity, so delivery follows [indirect participant-content retrieval](./Publishing%20and%20Delivery.md).

An Avatar image, name, or signature from its Publisher does not prove the identity of the person wearing it.

## Usage Rights

Basic Avatar use does not require a purchase certificate. Optional Entitlements and community acceptance rules follow [Commerce and Usage Rights](./Commerce%20and%20Usage%20Rights.md), independently of Avatar selection and the published content files. Missing proof is not proof of theft, and verification does not make a locally rendered model impossible to extract.

Stores may offer editable sources, a Portable Archive, service-dependent use, or a combination. A retained archive can preserve the promised independent use; a viewer cache or an entry in an Avatar Collection is not that promise. Backup and account-transfer conditions are disclosed by the offer rather than inferred from the file's presence.

## Current Proposals

- Make Avatar Core declarative and mandatory.
- Define a Humanoid Rig by evaluating VRM 1.0 before creating a new schema.
- Defer arbitrary Semantic Rig behavior until common semantic targets and retargeting tests exist.
- Standardize self-scoped Avatar Behavior on the common Resource Sandbox and behavior ABI.

Rig schema, behavior host API, expression mapping, physics, attachments, and performance classes are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Items: [Items and Ownership](./Items%20and%20Ownership.md)
- Inputs: [Devices and Input](../Client%20Platform/Devices%20and%20Input.md)
- Safety: [Safety and Moderation](../Trust/Safety%20and%20Moderation.md)
