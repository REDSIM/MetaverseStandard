# Features and Permissions

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> Compatibility answers “can this Client do it?” Permissions answer “may this application use it?”

## At a Glance

- **Features** describe supported functions, while **Limits** describe their numeric boundaries.
- **Input Signals** describe semantic input available from users or devices.
- **Permissions** protect sensitive data or actions.
- A Manifest declaration is only the maximum an application may request. It is not a Permission Grant.
- Missing optional Features use fallbacks. Denied Permissions remain denied without breaking unrelated behavior.

## Canonical Distinctions

| Term | Question | Example |
| --- | --- | --- |
| **Client Profile** | Which baseline contract is implemented? | Immersive XR |
| **Feature** | Can the Client technically do this? | spatial mesh support |
| **Limit** | How much can it do? | maximum texture dimension |
| **Input Signal** | Which semantic input can be supplied? | left-hand grip pose |
| **Permission** | Is this action or data sensitive? | read camera frames |
| **Permission Grant** | Has the user or policy allowed it here? | camera allowed once |
| **Capability Handle** | What runtime object provides the approved access? | scoped camera stream handle |

The last term is for implementers. Ordinary user-facing documentation can simply say “approved access.”

## Feature Negotiation

A Release declares:

- required Client Profiles.
- required Features and minimum Limits.
- optional Features.
- variants selected by Features or Limits.
- a fallback for each optional path.
- an explanation when no safe fallback exists.

The Client evaluates a bounded, verified Manifest and any needed dependency Manifests before downloading or expanding heavy content, compiling shaders, or starting untrusted behavior:

1. Check schema and Profile versions, supported formats, required Features, and minimum Limits.
2. Find complete compatible paths through the declared [Resource Variants: One Release, Optional Variants](../Concepts/Resource%20Model.md) and fallbacks.
3. Select a path within local memory, download, and execution budgets. Quality and energy preferences may influence the choice.
4. Fetch and validate only the selected files and dependencies. Actual decoded sizes and workloads are checked again. Declarations are not proof.
5. Use a declared fallback or report a structured incompatibility if preparation fails. Never activate a partially satisfied path.

The future selection specification needs exact requirement matching, alternative grouping, dependency rules, and failure reasons. Clients may choose different quality levels while agreeing on which paths are compatible.

Feature names are versioned and registered. An unknown required Feature makes the affected path incompatible. Launch is blocked only if no complete compatible path remains. Unknown optional Features are treated as unsupported.

### Optional service assistance

Local selection is the default and works with static hosts, Mirrors, caches, and offline files. No content-selection handshake is required. An online session may still require its own join handshake.

A service may help select a declared variant using a minimal compatibility summary: supported Profile versions, relevant Features, accepted encodings, and conservative Limit tiers. The Client verifies the result against the Manifest and local policy. An OS name or graphics API name alone is not a compatibility contract.

Hardware model, driver version, full Client version, and native graphics API are not mandatory disclosures. An explicitly scoped extension may negotiate target-specific details when necessary. The base flow exposes privacy-bucketed results instead of a complete hardware fingerprint.

### Runtime adaptation

Behavior may query the standardized Feature and Limit view to reduce quality or disable optional effects. It can react to changing budgets and device availability through the host API. Every newly loaded content path is checked before use. Runtime adaptation does not replace preflight and cannot rescue an unsupported format or memory exhaustion during initial loading.

## Permission Flow

1. The Manifest declares every Permission the Resource may request.
2. The Resource reaches a user action that needs access.
3. It asks the Client for a defined scope, duration, and purpose.
4. The Client checks for an existing matching grant or policy decision. Neither can exceed the Manifest's declared scope.
5. If a new user decision is needed, trusted System UI shows the application, data, recipient, duration, and choices. The user may deny access.
6. The Client creates a narrow Capability Handle or returns a structured denial.
7. The user can inspect and revoke the grant later.

An undeclared Permission request is always rejected. Automatically downloaded participant content and background Resources cannot create prompts.

Approval is not requested for every API call, frame, or network packet. Matching grants are reused within their scope and lifetime. Stored denials, prompt rate limits, and foreground user-action requirements prevent repeated requests from becoming pressure to consent. The user can change these decisions in Client settings.

## Permissions Versus World Authority

A Permission controls access to the local user's sensitive data, device, or external service. It never authorizes code to control another participant or arbitrary World state.

Avatar and Item behavior begins with a Self Handle inside its Resource Sandbox. The World may expose typed integration interfaces, but it validates every request and decides which state owner may apply it. For example, camera access cannot turn an Item into an authority that may call `setPosition` on another player.

Automatically received Resource Behavior may use the standard self-scoped API without prompting. Sensitive Permissions are considered only when the local user explicitly activates or equips that Resource and performs a foreground action. A Resource worn by somebody else cannot cause prompts on the viewer's Client.

## Scope and Duration

A Permission Grant is limited along relevant dimensions:

- **operation**: read, write, capture, transmit, discover, or control.
- **object**: selected file, device, contact group, spatial region, or service.
- **duration**: once, while in use, current session, or persistent.
- **destination**: local processing or named remote operator.
- **precision**: approximate position versus precise pose, for example.
- **context**: one World, Profile, Item integration, or Overlay App.

Persistent grants bind to the Application Principal, verified publisher key lineage, security epoch, Permission name, scope, and remote recipient. Updates inherit only what the principal rules explicitly allow. Grants do not transfer through embedding, equipping, copying, or dependency relationships.

The available durations depend on the Permission and OS policy. “Once” needs an operation-specific definition: one photo capture is different from a live camera stream. Persistent approval does not override background restrictions or remove active-capture indicators.

## Changes During Use and Updates

| Change | Result |
| --- | --- |
| An already declared optional feature is used for the first time | Request a scoped grant at that moment. No new Manifest is needed |
| The user selects another camera within a declared user-selected-device scope | Re-evaluate the device grant. No new Release is needed |
| The application needs access beyond its declared maximum | Publish a new verified Release under the current model. Do not edit the active Manifest in place |

Requests are checked against the Release actually running and its Application Principal. A newly downloaded Manifest cannot give old running code extra authority. Updates may reuse unchanged Content Files, so changing the Manifest does not require downloading every asset again.

For an update, the Client compares requested access and retains only grants allowed by the principal's inheritance rules. Newly required scope needs a new approval when used, unless an explicit managed policy already authorizes it. Declaring optional access does not trigger all its prompts during installation.

This is the current **exhaustive-declaration** model. A separately authenticated live-amendment mechanism is an alternative to evaluate, not an existing bypass. Its activation, rollback, and consent rules remain in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Permission Families

The registry will likely need separate entries for:

- camera frames and passthrough composition.
- microphone capture and voice transmission.
- eye, face, body, hand, and biometric tracking.
- spatial mesh, planes, anchors, depth, and room boundaries.
- selected files and application storage.
- clipboard and local application integration.
- network destinations, local-network discovery, Bluetooth, and nearby devices.
- location and environmental sensors.
- Profile fields, Account Claims, contacts, friends, private messages, social actions, and Entitlements.
- active World and session context.
- notifications, background execution, audio, and haptics.

Broad labels such as “hardware access” are insufficient. Camera access, spatial geometry, and passthrough composition reveal different data and need different controls.

Ordinary connections to authenticated World Services named by the publication may be covered by the Client's normal World network policy instead of producing a prompt for every packet. Dynamic third-party destinations, local-network access, and transmission of separately protected data require explicit scope. The Client still shows which operator the World contacts.

Supporting a transport is a Feature, not approval to contact any address. The [Network Broker](./Networking.md) applies destination, credential, and lifecycle rules equally to standard and custom protocols. A transport handshake does not grant access to tracking, voice, identity, or World state.

A firewall or antivirus may add protection, but does not replace these Client checks. It generally cannot distinguish the principals and grants inside one Client process. Clients may differ in presentation and apply stricter policy, not omit the shared isolation and authorization boundary.

## Cameras and Tracking

Access is separated by what the application receives, not just which physical device produced it:

- raw frames from a selected webcam or headset camera.
- derived eye gaze, face expressions, hand joints, or body poses.
- spatial mesh or depth data.
- passthrough composition without exposing raw camera frames.

Permission to use face tracking does not include its source camera images. Permission for one camera does not include all cameras. Trusted Client UI selects the device and supplies a scoped handle. Private device identifiers do not belong in a published Manifest. Unavailable or OS-restricted sources produce a clear failure, not a fallback to a more intrusive source.

An [AR World: XR and Passthrough](./Client%20and%20Runtime.md) does not require raw camera access merely to display virtual content over reality. Spatial data and geolocation remain separate declared scopes. Grants cover only the approved operation and lifetime. Persistent approval does not mean permanent background capture or permission to publish the environment to other participants.

These scopes control what Client APIs disclose. Once a World receives raw images, it may infer geometry, information about people, or location from those images without calling a dedicated spatial or geolocation API. Separate API permissions cannot prevent all such inference. Camera consent therefore explains the sensitivity of the image content, not only the name of the device.

## Local Processing and Network Export

The Client can enforce separate capture and export operations when it retains the data behind an opaque handle. It cannot reliably recognize every later transmission of data already exposed as arbitrary bytes to application code.

If a World receives raw camera frames and can send arbitrary bytes to its server, it may encode those frames into otherwise permitted traffic. A “local only” label does not prevent this. Technical local-only processing requires Client-owned operations or an isolated processing context with controlled outputs, including storage and inter-application communication. Temporarily disabling the network is insufficient if code can save data and send it later.

Custom raw-data processing remains possible with informed, scoped approval. The Client explains that the application's approved outgoing channels can carry those data. An application promise not to transmit them is not an enforceable guarantee. Exact mediated capture and export interfaces are still proposals.

## Example: World Photo Booth

This is an illustrative mediated flow, not an API schema. The World runs without camera access. When the user activates the booth, it requests a Client-managed capture from a user-selected camera. The Client handles consent and preview and retains the photo behind an opaque handle. Denial closes the photo feature while the rest of the World continues.

Exporting that photo to the World or a remote recipient is a separate approved operation. Arbitrary World-side processing of raw pixels follows the limits above. It does not inherit the mediated flow's local-only guarantee. A promise that a recipient will delete the image is a **Data Use Notice**, not proof of deletion.

## Background and Multi-World Rules

- Only the foreground, user-interacted Resource may prompt.
- Microphone, camera, tracking, location, and spatial data stop when the approved lifecycle scope ends.
- Persistent background access has a visible indicator and a direct revoke control.
- Switching Worlds does not silently redirect an existing stream to a new recipient.
- A suspended World holds no live Capability Handles unless the Permission explicitly defines Client-managed continuation.

## Failure Rules

- Denial returns a standard error, not a fake empty success value when that could mislead behavior.
- Revocation closes active handles promptly and sends a lifecycle event.
- If a Feature disappears, such as a disconnected tracker, the Client reports a state change and the Resource falls back.
- A Client may enforce stricter policy than a Manifest requests.

## Current Proposals

- Maintain separate registries for Profiles, Features, Limits, Input Signals, and Permissions.
- Follow [WebGPU](https://www.w3.org/TR/webgpu/)’s general model of required features plus numeric limits, with privacy-preserving buckets.
- Use runtime handles so approved access is explicit and revocable instead of being a global boolean.
- Adapt the separation between permission state and feature-specific access in the [W3C Permissions specification](https://www.w3.org/TR/permissions/), and device selection and capture controls in [Media Capture and Streams](https://www.w3.org/TR/mediacapture-streams/). These are references, not claims of identical APIs or universal device support.

The baseline registries, grant persistence, managed policy, prompts, and synchronization behavior are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Runtime owner: [Client and Runtime](./Client%20and%20Runtime.md)
- Device signals: [Devices and Input](./Devices%20and%20Input.md)
- Privacy: [Security and Privacy](../Trust/Security%20and%20Privacy.md)
