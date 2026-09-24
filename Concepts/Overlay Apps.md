# Overlay Apps

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> An Overlay App is a user-installed application that can present tools or information across Worlds without becoming part of those Worlds.

## At a Glance

- Overlay Apps are launched and controlled by the user through the Client.
- They are private to the user by default. A World does not automatically see them.
- Access to the active World, friends, camera, files, input, or other data requires narrow Permissions.
- Untrusted Overlay UI is shown inside a Client-controlled frame so it cannot be confused with trusted System UI.
- Automatic participant content is never allowed to install or launch an Overlay App.

## Examples

- a floating video or music panel.
- accessibility captions and translation.
- a calendar, notes, or communication tool.
- streaming controls.
- a World statistics panel using an approved interface.
- developer diagnostics.

These are non-normative examples. The UI and interaction profile is still an open design area.

## Runtime Model

An Overlay App is its own Application Principal, storage area, lifecycle, and permission context. It may continue while the user switches between Worlds, subject to background limits.

The Client can launch it from System UI, a shortcut, a gesture, or an explicit deep link. The Overlay App cannot register an unbounded input capture or hide the Client’s emergency controls.

Possible presentation forms include:

- a Client-framed 2D panel.
- a bounded 3D surface or object.
- an accessibility compositor layer.
- audio-only or background service output.

The first profile should choose a small portable UI model rather than allowing arbitrary native windows.

## World Context

An Overlay App begins with no knowledge of the active World. It may request specific context, for example:

- World Resource ID and display name.
- session population count.
- the user’s own position or selected object.
- a named World integration interface.
- a user-selected Profile or friend subset.

The Client shows the recipient and scope before granting access. A World may separately approve a two-way integration. Communication uses a typed, versioned interface. It does not expose the World’s memory or unrestricted event stream.

## Trusted UI Boundary

A malicious World or Overlay App can draw a picture that resembles a permission dialog. The Client therefore needs an authenticity mechanism that content cannot reproduce, such as a secure-attention button or gesture, a protected compositor layer, a persistent system indicator, and a guaranteed emergency exit.

“Client-framed” means the Client visibly marks untrusted Overlay content. It does not make the content trusted.

## Permissions and Background Use

- The Manifest declares the maximum Permissions the Overlay App may request.
- Prompts occur only while the user is interacting with the Overlay App.
- Camera, microphone, raw tracking, spatial data, files, friends, and World context are separate Permissions.
- Background execution, audio, and data access have visible indicators and independent controls.
- Grants are not inherited from the foreground World or another Overlay App.

## Failures and Policy

- A crashed Overlay App does not affect the World or Client Core.
- Unsupported presentation uses a simpler panel or disables the app clearly.
- The Client may throttle or suspend background Overlay Apps.
- Worlds may restrict what shared output an Overlay can inject, but cannot inspect private Overlay content without a grant.
- The user can close, mute, reset, or uninstall an Overlay App from trusted System UI at any time.

## Current Proposals

- Begin with a Client-framed panel profile and typed context interfaces.
- Treat 3D overlays, shared overlays, and compositor-level accessibility as later profiles.
- Keep Overlay Apps out of the first minimal interoperability milestone.

The portable UI model, deep links, World integration, shared presentation, and background services are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- Runtime: [Client and Runtime](../Client%20Platform/Client%20and%20Runtime.md)
- Permissions: [Features and Permissions](../Client%20Platform/Features%20and%20Permissions.md)
- Security: [Security and Privacy](../Trust/Security%20and%20Privacy.md)
