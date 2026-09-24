# Devices and Input

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> A World should be able to use a steering wheel, tracker, or custom sensor through the same permission boundary, without taking control of the user's hardware.

## At a Glance

- Applications consume typed Input Signals through the Client's Device Broker.
- Device Adapters connect existing hardware protocols and vendor APIs to that common interface.
- The model covers ordinary controls, XR tracking, specialist sensors, and approved device outputs.
- Connecting a device and allowing an application to use it are separate decisions.
- Applications can observe their authorized devices becoming available, unavailable, or invalid during use.
- Broad hardware support does not mean every device works on every Client or OS.

External programs use [Software Integration](./Software%20Integration.md). A streaming tool or animation editor is not a physical device merely because it sends data to the Client.

## Layered Model

```text
Physical device
      ↓
OS device API, existing protocol, or vendor SDK
      ↓
Sandboxed Device Adapter
      ↓
Client Device Broker
      ↓
Scoped Input Signals and controlled output requests
      ↓
Approved World, Resource Behavior, or Client function
```

The same model can support keyboards, mice, touch, gamepads, joysticks, steering wheels and pedals, musical controllers, trackers, accessibility devices, environmental sensors, and custom equipment. Output devices may offer vibration, force feedback, lights, or other controlled actions.

A Client or installed adapter advertises which interfaces and protocol versions it actually supports. USB and Bluetooth are connection technologies, not guarantees that the Client understands every device using them. An adapter may translate a vendor protocol into common signals or expose a bounded, documented extension for specialist applications.

Applications receive approved handles, not OS device handles or unrestricted sockets. Opening a World does not download or install a raw driver. Drivers and privileged vendor components remain separately installed and controlled by the OS or Client administrator.

## Input Signals

A signal definition includes:

- namespaced identifier and schema version.
- data type, units, range, and coordinate space.
- timestamp and clock domain.
- update behavior and expected rate.
- confidence, validity, and tracking state.
- privacy class.
- fallback or absence behavior.

Candidate families include poses, actions and buttons, analog axes, steering and pedal input, skeletal joints, face expressions, eye gaze, environmental measurements, and accessibility commands. Output requests are separate operations, not Input Signals.

Signals describe intent where possible. For example, a primary action is more portable than “button 7.” A specialist application may request approved device-specific channels with explicit types, bounds, units, and permissions. Supplying a custom schema neither installs executable code nor grants more access.

Unknown optional signals are ignored or use a declared fallback. A required signal with an unsupported schema cannot be used. The application reports the affected function as unavailable rather than guessing its meaning.

For [AR Worlds: XR and Passthrough](./Client%20and%20Runtime.md), spatial interfaces also need explicit reference spaces, transforms, units, timing, and tracking validity. A device may adjust or reset its local origin. The Client reports this rather than treating local coordinates as permanent geographic positions. Camera processing needs supported image geometry and timing, while geolocation needs an accuracy estimate. Exact interfaces remain open. Mapping, recognition, and shared-space alignment algorithms are not prescribed.

## What Is Available Right Now

Feature support and live input are different facts:

| Question | Example |
| --- | --- |
| Does the Client implement this interface? | The Client supports steering input |
| Is an approved source available to this application? | The user selected a connected wheel |
| Is the source supplying valid input now? | The wheel is active and calibrated |

A Client can support an interface while no device is connected. A connected device may be unapproved, busy, suspended, or temporarily unable to track. No input is a valid state, not a zero-valued sample or an instruction to keep applying the last value.

The proposed Broker interface provides an authorized-device snapshot and change events for connection, removal, grant revocation, suspension, and relevant state changes. Input samples separately carry freshness and validity. Reconnection rechecks the existing grant and device association. A different device is not silently substituted.

Applications see only sources and changes within their approved scope, not an inventory of all attached hardware. Without discovery approval, an unavailable source does not disclose whether a hidden device exists. Background observation follows the application's lifecycle grant.

### Descriptors and privacy

For an approved device, a descriptor identifies the supported protocol or interface versions, available signal schemas, permitted output operations, relevant Limits, and current access state. It uses an opaque application-scoped identifier instead of a globally stable hardware identifier. Protocol support is explicit, not inferred from the device's display name.

Manufacturer, model, serial number, and other identifying metadata are separate disclosures, available only when necessary under scoped consent. Most applications need a control layout or signal schema rather than an exact hardware identity. Unavailable metadata does not make a compatible signal unusable, and a manufacturer string is not proof of authenticity.

This applies the minimization approach in [W3C fingerprinting guidance](https://www.w3.org/TR/fingerprinting-guidance/). Even device-change events and unusual combinations of signals can reveal identifying information, so detailed descriptors are not part of the default public handshake.

## Device Approval Versus Application Access

The Client first authorizes an adapter to access a selected device, within OS restrictions. Each application then receives its own approved operations for that device. A Manifest declares the requested scope, not a blanket “hardware access” permission.

For example:

1. the user pairs a face tracker with the Client.
2. the adapter may read only that device.
3. the Client reports face-expression support and the authorized source's current availability separately.
4. an Avatar retargeter may use local signals under Client policy.
5. a World needs a distinct Permission to receive face data. Client-managed transmission also requires an approved recipient.

Pairing never creates a blanket grant to all Worlds, Items, Avatars, or Overlay Apps. Reading steering input does not authorize force feedback. Reading derived tracking does not authorize raw camera frames. Grants bind to the Application Principal, selected device scope, operation, and lifetime under [Features and Permissions](./Features%20and%20Permissions.md).

Trusted System UI handles selection and revocation. Existing matching grants can be reused without prompting for every sample. Automatically received participant content cannot prompt for devices, and child Resource Behavior does not inherit its World's grants.

Derived expressions and raw tracker-camera frames are separate access scopes. After raw data reaches code with arbitrary network access, the Client cannot recognize all subsequent encodings of those data. See [local processing and export boundaries: Local Processing and Network Export](./Features%20and%20Permissions.md).

## Adapter Security

A signed or authenticated adapter can still be buggy or malicious. Device discovery and a familiar vendor name do not establish trust. The adapter boundary provides:

- access only to assigned devices, interfaces, and operations.
- no general filesystem or local-network access.
- bounded CPU, memory, bandwidth, and message rates.
- explicit schema validation at the Broker boundary.
- revocable pairing and visible activity.
- isolation from Account credentials and other applications.

Native vendor libraries should be kept outside the Client Core whenever possible. Device messages and descriptors are validated as untrusted input. A crash or malformed report disables the affected adapter or source without granting fallback access to unrestricted hardware.

## Outputs and Physical Safety

Haptics, steering force, and other actuation use separate permissions and bounded requests. The Client and adapter enforce supported ranges, duration, update rate, and shared budgets. Several Worlds cannot independently multiply the permitted force or intensity.

Device-specific safe-stop behavior applies on revocation, disconnect, adapter failure, or an expired command lease. Foreground changes stop outputs unless a suitable background grant explicitly allows continuation. The user retains an immediate stop control outside World UI.

The safe state depends on the device. Software cannot promise to stop hardware after power loss or a broken connection. Equipment requiring stronger guarantees needs appropriate hardware safeguards and a tested adapter profile, or remains unsupported. Generic output access is not a safety certification.

## Existing Protocols

The first version should adapt existing interfaces rather than replace them:

- [OpenXR interaction profiles](https://registry.khronos.org/OpenXR/specs/1.1/html/xrspec.html) for common XR controllers and tracked interaction.
- **[HID](https://www.usb.org/hid)** for standard input hardware.
- **[MIDI](https://midi.org/specs)** for musical and control devices.
- optional **[OSC](https://opensoundcontrol.stanford.edu/spec-1_0.html) compatibility** where existing tracking tools or devices use it.
- platform [Bluetooth](https://www.bluetooth.com/specifications/specs/), [USB](https://www.usb.org/documents), sensor, and accessibility APIs.
- narrowly scoped vendor SDK adapters.

OSC is neither the required hardware transport nor an authentication mechanism. Legacy endpoints need explicit configuration and restricted access. General software-to-software OSC compatibility belongs in [Software Integration](./Software%20Integration.md).

A new device wire protocol is justified only when existing options cannot express a required lifecycle, discovery, security, or timing property.

### Web platform references

- [Gamepad](https://www.w3.org/TR/gamepad/) and [WebXR](https://www.w3.org/TR/webxr/) inform input mapping, authorized exposure, and connection or input-source changes.
- [WebHID](https://wicg.github.io/webhid/) and [WebUSB](https://wicg.github.io/webusb/) inform user-selected device access and restrictions around lower-level device interfaces.

These are semantic references, not a claim of API compatibility or universal browser support. The proposed Broker adds common typed signals and scoped outputs rather than exposing all lower-level operations to every World.

## Fallback and Portability

- A device with no supported adapter is unavailable. The Client may explain what support is missing, but does not run a downloaded driver or expose raw bytes as a fallback.
- A known generic control interface may still work without vendor-specific features. User-confirmed mapping can assign its controls to supported actions.
- Missing full-body tracking can fall back to procedural or partial-body animation.
- A disconnected device emits a clear validity transition, not frozen “live” data.
- Clients may resample signals but preserve timestamp and confidence semantics.
- Worlds declare required Input Signals only when no meaningful fallback exists. Failure disables the affected function or prevents entry to the device-dependent mode with an explanation. It does not forbid retrieving the files or running a compatible base path. See [Features and Permissions](./Features%20and%20Permissions.md).
- Calibration is owned by the Client or adapter and is shared with a Resource only when necessary.

## Current Proposals

- Standardize the Device Broker interface, versioned signal definitions, and supported-protocol descriptors first.
- Separate static Feature support, authorized device availability, and per-sample validity.
- Test hotplug, no-input operation, scoped discovery, replacement devices, revocation, and safe output termination across independent implementations.
- Use OpenXR semantics as input for poses and actions without making OpenXR the only backend.
- Retain an OSC adapter for compatibility, not as the universal native protocol.

Exact schemas, event ordering, coordinate conventions, adapter packaging, remote-device authentication, and device-specific output safety remain open in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md). The architecture allows these extensions without making every Client support every device.

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Compatibility and grants: [Features and Permissions](./Features%20and%20Permissions.md)
- Avatar mapping: [Avatars](../Concepts/Avatars.md)
- Isolation: [Client and Runtime](./Client%20and%20Runtime.md)
- External programs: [Software Integration](./Software%20Integration.md)
