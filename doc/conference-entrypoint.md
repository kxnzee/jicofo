# Conference entry point (focus service side)

This document describes the focus-service (Jicofo) code that receives a
client's initial conference request and creates or looks up the resulting
conference. It documents which code sends/receives the request; for the wire
format of the request itself (HTTP and XMPP payload shapes), see
[`conference-request.md`](./conference-request.md) — this document does not
repeat that content.

## Receiving the request

`ConferenceIqHandler.handleConferenceIq(query: ConferenceIq): IQ`
(`jicofo/src/main/kotlin/org/jitsi/jicofo/xmpp/ConferenceIqHandler.kt:80`,
inside the `ConferenceIqHandler` class starting at line 47) is the XMPP entry
point that receives the client's initial conference request as a
`ConferenceIq` (see `conference-request.md` for that IQ's format). It delegates
to the private `doHandleConferenceIq(query: ConferenceIq): IQ`
(`ConferenceIqHandler.kt:97`), which performs the room lookup and validation.

## Creating or looking up the conference

`FocusManager.conferenceRequest(...)`
(`jicofo/src/main/kotlin/org/jitsi/jicofo/FocusManager.kt:90`, inside the
`FocusManager` class starting at line 48) is the step that creates or looks up
the resulting conference. Its body (lines 100-117) looks up the requested room
in the `conferences` map under `synchronized(conferencesSyncRoot)`, and if no
conference exists yet, calls the private `createConference` (line 120), which
instantiates a `JitsiMeetConferenceImpl` at line 129.

## See also

- [`jitsi-meet/doc/conference-entrypoint.md`](https://github.com/kxnzee/jitsi-meet/blob/master/doc/conference-entrypoint.md) —
  documents the corresponding entry point on the client side.
- [`conference-request.md`](./conference-request.md) — the wire-format
  reference for the conference request this entry point receives (HTTP and
  XMPP payload shapes).
