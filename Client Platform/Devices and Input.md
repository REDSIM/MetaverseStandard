# Devices and Input

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> The standard should normalize what a device means to an application before inventing a new way to transport its bytes.

## At a Glance

- Applications consume semantic Input Signals, not vendor-specific packets.
- A Device Broker maps OpenXR, HID, MIDI, OSC, Bluetooth, USB, and vendor SDKs into those signals.
- Connecting a device and allowing a World to use its data are separate decisions.
- Device Adapters are untrusted or semi-trusted plugins with narrow OS privileges and strict limits.
- Unsupported or disconnected input produces a predictable fallback.

## Layered Model

```text
Physical device
      ↓
Existing transport or vendor API
      ↓
Sandboxed Device Adapter
      ↓
Client Device Broker
      ↓
Standard Input Signals
      ↓
World, Avatar retargeting, or Overlay App
```

This keeps a tracker vendor from defining application semantics and keeps Worlds from speaking directly to arbitrary hardware.

## Input Signals

A signal definition includes:

- stable name and version;
- data type, units, range, and coordinate space;
- timestamp and clock domain;
- update behavior and expected rate;
- confidence, validity, and tracking state;
- privacy class;
- fallback or absence behavior.

Candidate families include poses, actions and buttons, analog controls, skeletal joints, face expressions, eye gaze, locomotion, audio controls, haptic requests, environmental measurements, and accessibility commands.

Signals should describe intent where possible. `user.action.primary` is more portable than “button 7,” while raw device channels may remain available to explicitly authorized specialist applications.

For [AR Worlds — XR and Passthrough](./Client%20and%20Runtime.md), spatial interfaces also need explicit reference spaces, transforms, units, timing, and tracking validity. A device may adjust or reset its local origin; the Client reports this rather than treating local coordinates as permanent geographic positions. Camera processing needs supported image geometry and timing, while geolocation needs an accuracy estimate. Exact interfaces remain open; mapping, recognition, and shared-space alignment algorithms are not prescribed.

## Device Pairing Versus Application Access

The Client first asks whether a Device Adapter may connect to a particular device or transport. Later, each Resource separately requests relevant semantic data.

For example:

1. the user pairs a face tracker with the Client;
2. the adapter may read only that device;
3. the Client exposes `face.expression.*` as supported Input Signals;
4. an Avatar retargeter may use local signals under Client policy;
5. a World needs a distinct Permission to receive face data; Client-managed transmission also requires an approved recipient.

Pairing never creates a blanket grant to all Worlds.

Derived expressions and raw tracker-camera frames are separate access scopes. After raw data reaches code with arbitrary network access, the Client cannot recognize all subsequent encodings of those data. See [local processing and export boundaries — Local Processing and Network Export](./Features%20and%20Permissions.md).

## Adapter Security

A signed or authenticated adapter can still be buggy or malicious. It receives:

- access only to assigned devices or endpoints;
- no general filesystem or local-network access;
- bounded CPU, memory, bandwidth, and message rates;
- explicit schema validation at the Broker boundary;
- revocable pairing and visible activity;
- isolation from Account credentials and other applications.

Native vendor libraries should be kept outside the Client Core whenever possible.

## Existing Protocols

The first version should adapt proven protocols rather than replace them:

- [OpenXR interaction profiles](https://www.khronos.org/openxr/) for common XR controllers and tracked interaction;
- **HID** for standard input hardware;
- **MIDI** for musical and control devices;
- **OSC compatibility** for existing social-XR and creator ecosystems;
- platform Bluetooth, USB, sensor, and accessibility APIs;
- narrowly scoped vendor SDK adapters.

A new wire protocol is justified only when multiple transports cannot express a required common lifecycle, discovery, security, or timing property.

## Fallback and Portability

- Missing full-body tracking falls back to procedural or partial-body animation.
- A disconnected device emits a clear validity transition, not frozen “live” data.
- Clients may resample signals but preserve timestamp and confidence semantics.
- Worlds declare required Input Signals only when no meaningful fallback exists.
- Calibration is owned by the Client or adapter and is shared with a Resource only when necessary.

## Current Proposals

- Standardize the Device Broker API and semantic signal registry first.
- Use OpenXR semantics as input for poses and actions without making OpenXR the only backend.
- Retain an OSC adapter for compatibility, not as the universal native protocol.

Signal schemas, coordinate conventions, adapter packaging, discovery, remote devices, and haptic safety are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Compatibility and grants: [Features and Permissions](./Features%20and%20Permissions.md)
- Avatar mapping: [Avatars](../Concepts/Avatars.md)
- Isolation: [Client and Runtime](./Client%20and%20Runtime.md)
