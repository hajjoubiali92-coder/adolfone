# Android redesign — implementation plan

This document is the acceptance checklist for the next Android port revision.
It deliberately separates working controls from features that are not wired to
the 0 A.D. engine yet. A visual mock or a hard-coded server list is not a
multiplayer implementation.

## Target device and build constraints

- Android 10+ (API 29), ARM64 only.
- Primary test device: Motorola Moto G22.
- Landscape, full-screen, touch-first interface.
- Keep the current native Pyrogenesis engine and the existing reproducible
  GitHub Actions build. Do not replace the game with a WebView or a mock.
- Keep the 3D scene resolution reduction and low-memory defaults until
  measurements on the target phone justify changing them.

## Interaction requirements

- Keep the existing SDL/native touch path for ordinary game input.
- Terrain drag pans the camera; pinch zooms; two-finger twist rotates.
- Unit/building tap selects and refreshes the original game action HUD.
- Drag-box selection must remain distinct from camera panning and must not
  accidentally issue a move order.
- Add a contextual radial menu on a deliberate long-press, with context
  derived from the picked entity (terrain, unit, building, or group).
- Add the requested compact active-tool indicators in the upper-right; tapping
  an indicator turns that mode off.
- When one or more units are selected, show a right-side action panel using
  roughly 40% of the viewport. The panel must use real 0 A.D. selection and
  action data, not a separate fake unit model.
- The panel must not cover the map selection area or steal touches from the
  native game HUD when it is closed.

## Multiplayer requirements

The current Android native build defines `CONFIG2_LOBBY=0`. It includes ENet,
but does not compile the XMPP lobby dependency. Consequently the existing APK
does not provide the real online lobby and cannot truthfully advertise
PC/Android online crossplay.

Before adding the requested **VEDI SERVER** and **VEDI GIOCATORI** screens:
1. Restore and build the actual 0 A.D. lobby dependency and its TLS trust setup.
2. Confirm that the selected 0 A.D. 0.28.0 lobby service still accepts this
   client version and protocol.
3. Populate server and player lists from real lobby/network responses.
4. Request a password only when the selected server advertises protection.
5. Verify a real Android-to-PC match, including joining, gameplay, and leaving.

A LAN/direct-IP browser is a separate feature and must not be presented as the
online lobby. No sample/fake servers or players are allowed in release UI.

## Current verified baseline

The repository already contains a native gesture recognizer and SDL adapter:
`native/TouchGestures.h`, `native/AndroidTouchInput.cpp`, and
`GameActivity.java`. The current recognizer already handles camera pan,
pinch/twist, two-finger deselection, long-press drag-box selection, tap
selection, similar-unit double tap, and build placement confirmation.

The current build profile targets 30 FPS and a 480-pixel short edge for the
3D scene. These are defaults, not measured performance results on the Moto
G22.

## Release gates

Do not call the redesign complete until all of these pass:

- Native gesture tests and session-input tests.
- Full GitHub Actions APK build and APK integrity verification.
- Install/update test on the Moto G22 without wiping app data.
- Enter a skirmish, select units, issue move/gather/build/attack commands,
  use the radial menu and right-side panel, save and reload.
- Background/resume and second-session relaunch.
- Real multiplayer server/player discovery and a password-protected join.
- A completed Android-versus-PC match before claiming crossplay.

The existing branch is a development branch. The default `main` branch and
the user's currently installed APK are left unchanged.
