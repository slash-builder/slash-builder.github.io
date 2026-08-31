---
layout: post
title: "benix-base: safe by construction, compatible by contract"
date: 2026-08-31
categories: [benix-base]
tags: [benix-base, libbenix, rust, kernel-kit, abi]
excerpt: "A small cross-platform kernel-primitives crate — Thread, Area, Port, Semaphore — shipped as a safe Rust API and a stable C ABI at once. Same 17 symbols, verified running on Windows and inspected on macOS."
---

`benix-base` is a new, deliberately small kit: a portable set of
kernel primitives — **Thread**, **Area**, **Port**, and **Semaphore** —
that anything else can build on. The naming and the shape come straight
from the BeOS Kernel Kit; that lineage is the point, not decoration.
This is what the Kit model looks like when it ships as a real artifact.

**What shipped**: a C reference implementation and a Rust port
(`libbenix`) of the same four primitives, exporting one identical
C ABI. We built the C reference first — the fastest way to get the ABI
right is to write it in the language the ABI is defined in, then port.
The reference wasn't just compiled: it was cross-compiled to native
Windows with mingw-w64 and run for real on Windows 10, with every
primitive behaving as specified. Not "builds clean" — actually
executes, correctly, off its home platform.

The Rust port followed and exports the exact same **17-symbol C ABI**
as the C reference, with zero FFI overhead. Anything that links against
the C version links against the Rust version unchanged — no shim, no
recompile of the caller. We confirmed the exported symbol set two ways:
`nm`/`objdump` inspection on macOS, and again through a real Windows
cross-compile. The contract is the symbols, and the symbols match.

**Why it matters if you're building on the Kit model**: this is one
crate that is simultaneously a safe Rust API for Rust consumers and a
stable C ABI for everything else that needs to link against
kernel-style primitives. You don't choose between memory-safe and
linkable — you get both from the same build. "Safe by construction,
compatible by contract" isn't a slogan here; it's the deliverable.

And the safety earned its keep before this ever shipped. Porting to
Rust surfaced three real defects the C-only version could not have
caught on its own:

- a C-ABI parity mismatch in one function, exposed the moment two
  implementations had to agree on the same signature;
- a `windows-sys` feature-gating issue that would have quietly dropped
  a dependency surface; and
- a non-`Send` closure the Rust compiler rejected at compile time — a
  threading mistake C's type system has no way to see. In C it would
  have compiled cleanly and failed at runtime, on someone else's
  machine, on a thread boundary. Here it never linked.

That last one is the whole argument in miniature: the type system
turns a class of concurrency bug into a build error.

**On the pipeline**: none of this needed new plumbing. `benix-base`
lands on the same shared kit build-and-publish pipeline the studio's
other kits already ride. Zero new CI infrastructure — a new kit, an
existing conveyor.

**What this is and isn't**: this is a primitives crate, shipped and
verified cross-platform. It is not a platform announcement, and it
doesn't depend on one. If you need portable kernel-style primitives
with a stable ABI and a safe Rust surface, this is a thing you can
link against today. Watch
[github.com/slash-builder](https://github.com/slash-builder) for the
crate and its ABI reference.
