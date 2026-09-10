# Bridge control boundary (focus service side)

This document describes the `jitsi-control` (Jicofo) side of the boundary
between bridge selection and the `jitsi-videobridge` (JVB) instance that ends
up hosting a conference: which code picks a bridge, and which code sends the
resulting colibri2 control request to it. For the wire format of that
control request itself, see the existing
[`rest-colibri2.md`](https://github.com/kxnzee/jitsi-videobridge/blob/master/doc/rest-colibri2.md) reference
in `jitsi-videobridge` — this document does not repeat that content.

## Selecting a bridge

`JitsiMeetConferenceImpl` holds a `BridgeSelector` reference
(`jicofo/src/main/java/org/jitsi/jicofo/conference/JitsiMeetConferenceImpl.java:317`
constructor parameter, assigned to the `bridgeSelector` field at line 336) and
passes it to a `ColibriV2SessionManager` it constructs in
`getColibriSessionManager()`
(`jicofo/src/main/java/org/jitsi/jicofo/conference/JitsiMeetConferenceImpl.java:390`).

`ColibriV2SessionManager.doAllocate`
(`jicofo-selector/src/main/kotlin/org/jitsi/jicofo/bridge/colibri/ColibriV2SessionManager.kt:543`)
calls `BridgeSelector.selectBridge`
(`jicofo-selector/src/main/kotlin/org/jitsi/jicofo/bridge/BridgeSelector.kt:161-171`,
inside the `BridgeSelector` class starting at line 40) to pick the JVB
instance that will host the participant being allocated.

## Sending the control request to the selected bridge

The selected bridge is represented by a `Colibri2Session`
(`jicofo-selector/src/main/kotlin/org/jitsi/jicofo/bridge/colibri/Colibri2Session.kt:64`),
which builds the outbound colibri2 conference create/modify request in
`createRequest`
(`jicofo-selector/src/main/kotlin/org/jitsi/jicofo/bridge/colibri/Colibri2Session.kt:266`)
and sends it to the bridge via `sendRequest`
(`jicofo-selector/src/main/kotlin/org/jitsi/jicofo/bridge/colibri/Colibri2Session.kt:433`).

## Receiving side

`jitsi-videobridge` receives this request in a `ColibriQueue` request handler
on `Conference` and dispatches it to
`Colibri2ConferenceHandler.handleConferenceModifyIQ`, which creates or
updates the conference on that bridge. See
[`jitsi-videobridge/doc/bridge-control-boundary.md`](https://github.com/kxnzee/jitsi-videobridge/blob/master/doc/bridge-control-boundary.md)
for the full receiving-side detail, owned by that repository.

## See also

- [`jitsi-videobridge/doc/bridge-control-boundary.md`](https://github.com/kxnzee/jitsi-videobridge/blob/master/doc/bridge-control-boundary.md) —
  the receiving-side detail in the `jitsi-videobridge` repository.
- [`conference-request.md`](./conference-request.md) — the client-facing
  conference-request wire format; unrelated to the bridge-to-bridge control
  boundary documented here, listed for orientation only.
