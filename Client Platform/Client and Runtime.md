# Client and Runtime

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> The Client opens Worlds, mediates access to the device, and keeps untrusted applications isolated from the user and from each other.

## At a Glance

- Anyone may implement a compatible Client.
- An **App Client** runs as an ordinary application. A **System Client** may be the main shell of an XR device.
- The standard defines observable isolation and lifecycle behavior, not one mandatory process layout.
- Several Worlds may remain open, but presentation, execution, and session presence are managed separately.
- Only trusted System UI grants Permissions, manages identities, and provides emergency controls.

## Client Roles

[Architecture Overview](../Architecture%20Overview.md) places these local roles beside remote services and other participants. The table below describes Client responsibilities, not a required plug-in ABI or one operating-system process per row.

| Component | Role | Trust note |
| --- | --- | --- |
| **Client Core** | Owns principals, policy, lifecycle, grants, and isolation decisions | Small trusted computing base |
| **System UI** | Shows authentic prompts, identity state, indicators, and emergency controls | Cannot be replaced by World content |
| **World Runtime** | Executes one validated World and its components | Untrusted sandbox |
| **Resource Sandbox** | Runs one Avatar or Item behavior component with self-scoped handles | Nested untrusted sandbox |
| **Broker** | Provides narrow access to files, network, devices, identity, audio, and storage | Enforces grants and quotas |
| **Platform Adapter** | Maps operating-system, graphics, XR, and input APIs into standard interfaces | Privileged only as needed |

Parsers, media decoders, shader compilers, discovery, networking, and content storage should be isolated services rather than automatically becoming part of the Client Core. This reduces the amount of native code that must be trusted.

The [Network Broker](./Networking.md) exposes versioned binary I/O to sandboxed behavior. A World can implement custom application networking without replacing the broker or inheriting operating-system socket access. Replication and reconciliation remain application logic, not mandatory algorithms in the Client Core.

## Startup and Navigation

The Client's startup and navigation experience is described in [Finding Worlds](../Concepts/Finding%20Worlds.md). Address entry, Profile selection, saved Collections, and recovery controls remain available without a working home World. Their layout is a Client design choice, not a requirement to implement one standard menu.

## Client Profiles

Cross-platform compatibility does not mean every device implements every feature. It means a World can target a named baseline and receive predictable fallbacks elsewhere.

Candidate profiles include:

- **Core 3D**: baseline scene, input, audio, and sandbox needed by all Clients.
- **Desktop**: keyboard, pointer, larger memory and graphics budgets.
- **Mobile**: touch, mobile lifecycle, and stricter resource limits.
- **Immersive XR**: stereoscopic presentation, tracked poses, spatial input, and XR safety controls.

Profiles are bundles of minimum Features and Limits. A specific device may advertise more than its named Profile.

These candidate names describe supported behavior, not mandatory build targets or an OS detector. A device may implement several Profiles, and one [Release](../Concepts/Resource%20Model.md) may serve all of them. Content selection follows [Features and Permissions](./Features%20and%20Permissions.md), not a fixed desktop/mobile split.

## Isolation Requirements

The implementation may use one operating-system process per World, a process pool, virtual machines, language sandboxes, or a combination. What matters is observable containment:

- one World cannot read another World’s memory, storage, tokens, or Permission Grants.
- one Avatar or Item behavior component cannot inspect or mutate another Resource or participant without a typed World-mediated interface.
- downloaded code cannot call operating-system APIs except through brokers.
- CPU, memory, GPU, storage, network, audio, and message budgets are enforced.
- untrusted parsers and compilers are isolated and restartable.
- a World crash or forced termination leaves the Client and other Worlds usable.
- native compiled caches are local, disposable, and never accepted as portable authority.
- Client emergency controls remain responsive under load.

Avatars and Items do not each require a separate OS process. Their code may run as separate [WebAssembly components](https://github.com/WebAssembly/component-model) or equivalent compartments inside the World Runtime, with independent memory, principals, quotas, Permission Grants, and Self Handles. Sharing a scheduler or process never implies sharing authority.

## Multi-World Lifecycle

Concurrent Worlds are an optional Client Feature, not a requirement that every device run several Worlds. The architecture still avoids a global single-World assumption in presence, identity, and social operations. Clients expose their supported limits and handle resource pressure without pretending that every open view remains connected.

Three independent state axes avoid confusing “open,” “visible,” and “online.”

### Execution

- **Running**: normal execution budget.
- **Throttled**: reduced updates and background budget.
- **Suspended**: no Resource code executes, but Client-managed state may remain.
- **Terminated**: runtime is destroyed.

### Presentation

- **Foreground**: primary immersive view and input focus.
- **Visible**: shown as a panel, portal, or secondary surface.
- **Hidden**: no visual presentation.

### Session presence

- **Connected**: live participant.
- **Away**: Client-managed AFK presence. Resource code may be suspended.
- **Disconnected**: no session presence.

Each World Presence communicates its active Avatar to the relevant World or session. A Client may use one Avatar everywhere, allow different Avatars in simultaneous Worlds, or provide optional per-World presets. The standard does not require one selection policy.

For example, an interview World may be hidden and throttled while its audio remains connected, while a home World becomes foreground. The user may have a different Avatar in each. Whether an away Avatar freezes, uses an AFK pose, or receives limited pose updates is a user and session policy choice.

A practical switching default is to focus input on the selected World and mark the previous presence away. This does not pause the shared World for other participants. Session timeout and admission policies may still end a background connection. Social visibility and person-directed Join Requests follow [the multi-presence model: Multiple shared presences](../Concepts/Identity%20and%20Profiles.md), not one global current-World field.

## Focus and Data Arbitration

The Client separately controls:

- immersive visual focus.
- keyboard, controller, hand, and gaze input focus.
- microphone capture and destination.
- tracking publication.
- audio output and mixing.
- haptic output.
- camera and spatial-data access.

Live tracking is not copied into every open World by default. Background access needs a specific Permission and visible indication.

## XR and Passthrough

An **Augmented Reality (AR)** experience is an ordinary World that combines virtual content with the user's physical surroundings. It uses the same Release, scripts, storage, networking, and permission model as other Worlds, not a new Resource Type or a privileged built-in application. A third-party World can provide an AR home, shared workspace, or location-based experience.

The standard defines the interfaces a World can use, not one mandatory environment-mapping algorithm or AR service. No separate universal “AR engine” is required, but portable access to device presentation and spatial data still needs common contracts.

### Client services and World logic

| Responsibility | Owner |
| --- | --- |
| Device integration, view timing, supported blending modes, and protected safety UI | Client and underlying OS/XR runtime |
| Sensor and spatial interfaces, Feature checks, grants, and lifecycle limits | Client brokers |
| Holograms, interaction, custom scene interpretation, saved application state, and multiplayer rules | World scripts and any services the creator chooses |

Video passthrough combines a camera view with virtual content. Optical see-through lets the user see reality through the display itself. Neither implies that World code receives camera images. Where supported, a World can use Client-provided tracking and Spatial Anchors without implementing its own computer vision. With separately approved camera or sensor access, its scripts can instead implement custom recognition or mapping within normal sandbox budgets. They do not replace the trusted device compositor or safety controls.

The access categories remain separate:

- **AR presentation**: show virtual content together with the physical environment, without necessarily exposing images.
- **spatial data**: receive approved poses, surface queries, planes, meshes, anchors, or depth.
- **camera access**: receive frames from a selected supported camera.
- **geolocation**: receive an approved location estimate and its accuracy, not necessarily GPS data.

The Manifest declares required and optional Features and Permissions for the intended path. The Client checks availability and obtains scoped approval where needed, reusing matching grants rather than prompting per frame. A World requesting only presentation receives no implicit camera, room-scan, or location access. An existing grant does not make an unavailable or OS-restricted sensor accessible.

### Persistent placement

World scripts decide which objects and interactions to remember through ordinary scoped storage or approved backend services. A **Spatial Anchor** associates placement with the physical environment. Restoring the object record and finding the same physical place are different operations.

Persistent anchors are optional. Saved local coordinates or anchor identifiers do not by themselves promise restoration on another device or after tracking data is deleted. The World can use a supported anchor service, its own approved mapping logic, or ask the user to place the object again. No global map of users' rooms or universal anchor provider is required.

### Shared AR

A World can synchronize holograms through its usual custom networking or a chosen Session Profile. Co-located users additionally need an agreed mapping between their local tracking spaces and the shared physical space. Exchanging object positions, local anchor identifiers, or geographic coordinates alone does not establish accurate alignment.

The World may use user-assisted alignment, supported shared anchors, or an optional localization service. The algorithm, discovery service, and backend remain developer choices. Physical proximity does not automatically join a session, reveal identity, or publish room scans. Remote participation is also possible without claiming that everyone occupies the same physical place.

### Privacy, lifecycle, and failure

Camera images, room geometry, persistent place references, and location can reveal sensitive surroundings. Storage and sharing follow the World principal, chosen recipients, and [processing and export boundaries: Local Processing and Network Export](./Features%20and%20Permissions.md). Once scripts receive raw data and arbitrary outgoing channels, a “local only” label cannot guarantee that the data remain local.

Tracking loss, recentering, failed anchor restoration, permission revocation, and changes of foreground World need explicit state changes. Unreliable placement is hidden or visibly marked rather than presented as accurate. Background capture follows the ordinary grant and lifecycle policy. Switching Worlds does not silently transfer sensors or spatial maps to the next World.

Missing optional support may use manual placement, a flat view, or a non-AR mode where the creator provides one. If the physical-environment function is essential, the Client explains incompatibility or denied access. It does not silently substitute a more intrusive sensor. Trusted emergency exit and available device safety boundaries remain outside World control.

For example, a shared desk World may save notes and synchronize their contents while using approved surface placement. It requests raw images only for a separate feature that actually needs them. If it cannot find yesterday's desk position, it retains the notes and asks for placement again. This is an illustrative application flow, not a required AR implementation.

## App Client and System Client

An App Client can run on Windows, Linux, mobile systems, or existing headsets. A System Client may ship as the primary device interface on top of an operating system, similar in product role to a console or gaming shell.

Both expose the same application contracts. A System Client may integrate more deeply with hardware, but it does not give Worlds more authority. Client-as-system deployment is a future product profile, not a requirement for the first protocol version.

## Trusted System UI

Content can draw a fake dialog, so visual style alone cannot prove authenticity. A Client needs at least one secure-attention path that a World cannot intercept or imitate, plus a protected compositor layer, persistent recording/sensor indicators, and a guaranteed emergency exit.

## Current Proposals

- Specify isolation outcomes rather than requiring one process topology.
- Define Core 3D as the first conformance Profile and layer Desktop, Mobile, and Immersive XR above it.
- Use separate execution, presentation, and presence state machines.
- Use nested Resource Sandboxes for self-scoped Avatar and Item behavior.
- Make brokers and Application Principals the only route to privileged services.
- Adapt the AR presentation, reference-space, and optional sensor boundaries reviewed in [Web Platform Alignment](../Reference/Web%20Platform%20Alignment.md), while keeping environment interpretation and shared-AR application logic developer-defined.

Profile contents, lifecycle events, resource budgets, secure-attention behavior, and System Client integration are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Compatibility and consent: [Features and Permissions](./Features%20and%20Permissions.md)
- Network interfaces: [Networking](./Networking.md)
- World behavior: [Worlds and Sessions](../Concepts/Worlds%20and%20Sessions.md)
- Threat model: [Security and Privacy](../Trust/Security%20and%20Privacy.md)
