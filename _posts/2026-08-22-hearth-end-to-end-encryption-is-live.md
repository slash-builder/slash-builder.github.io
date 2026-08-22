---
layout: post
title: "Hearth end-to-end encryption is live"
date: 2026-08-22
categories: [hearth]
tags: [hearth, encryption, security]
excerpt: "Sealed payloads now move between paired devices with the hub never able to read them. Verified end-to-end against a live production fabric, not just in tests."
---

Hearth's relay was never meant to be a trusted reader of what it carries.
That's now proven, not just asserted.

**What shipped**: a paired-device sealing round trip, verified live against
a real production hub. A sender seals a payload to a recipient's public
key; the hub relays the sealed bytes without being able to open them; the
recipient unwraps it and recovers the exact original content,
byte-for-byte. This holds for the base key-exchange path and for real
application traffic — chat messages published and decrypted between two
independent connections, with 129 of 129 hard-fail cases behaving exactly
as specified.

**Why it matters if you're implementing against Hearth**: the relay is
correctly untrusted by construction, not by policy. A conformant
implementation doesn't need to trust the hub operator to keep a payload
private — the capability-grant model and the sealing scheme do that
regardless of who's running the relay. If you're building a client, this
is the behavior to test against; if you're operating your own hub, this
is the guarantee you're not on the hook for holding.

**What this doesn't cover yet**: the full Hearth specification — the
canonical `.proto`, the wire-format publication — is still being finished.
This post documents a proven capability, not a finished spec. Watch
[github.com/slash-builder](https://github.com/slash-builder) for the
publication itself.
