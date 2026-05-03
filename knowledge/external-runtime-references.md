# External Runtime References

Last updated: 2026-05-04

## Purpose

This note captures external reference points that are useful when shaping Open CAD's runtime and delivery strategy.

It is intentionally a design summary, not a code import log. The goal is to extract patterns and constraints without copying external implementations.

## Reference: OpenSCAD

Source:

- https://openscad.org/
- https://github.com/openscad/openscad/

Observed intent:

- programmer-oriented CAD modeler
- strong local-first distribution across desktop operating systems
- explicit documentation, tutorial, and library surface

Relevant takeaways for Open CAD:

- keep runtime expectations explicit
- maintain a strong local execution story, not just hosted demos
- document the modeling/runtime boundary clearly

## Reference: openscad.cloud

Source:

- https://openscad.cloud/

Observed intent:

- online editor backed by a WASM port of OpenSCAD
- browser execution as a first-class product surface

Relevant takeaways for Open CAD:

- a CAD runtime compiled to WebAssembly can be delivered directly in the browser
- online execution should be treated as a real deployment mode, not only a demo path
- the critical deployment concern is making the WASM asset reachable and initialized predictably

## Reference: opencad.dev

Source:

- https://opencad.dev/

Observed intent:

- browser-based, self-hostable CAD editor
- strong emphasis on zero-install access, import/export, and practical engineering workflows
- clear distinction between product UX and the underlying technology stack

Relevant takeaways for Open CAD:

- keep browser-first delivery simple
- make self-hosting and static deployment easy to understand
- distinguish the kernel from any future full editor or downstream application layer

## What Open CAD should apply

The repository should continue treating these as first-class runtime modes:

1. local development with a browser dev server
2. local preview of the production static bundle
3. hosted browser deployment with an explicit `wasmURL`

## What not to assume

- Open CAD is not a full end-user CAD editor in this repository
- external product features such as full 2D drafting UX, DXF-native editing, or cloud collaboration are references, not current repository capabilities
- external code should not be copied into this repository without an explicit implementation decision and license review