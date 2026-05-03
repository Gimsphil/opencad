# OpenGeometry Development Reference

Last updated: 2026-05-04
Status: Active working reference

## Purpose

This document is the long-lived development reference for work in this repository.

Use it to keep implementation work aligned with the current product direction,
runtime constraints, architecture boundaries, and verified repository behavior.

When code, workflows, or architectural assumptions change in a meaningful way,
append an entry to the update log at the end of this file.

## Scope

This file is intended to answer four practical questions during development:

1. What is OpenGeometry in this repository?
2. What parts of the stack are authoritative for geometry behavior?
3. What constraints must not be violated while extending the codebase?
4. What development directions fit the existing architecture best?

## Product Definition

OpenGeometry is a browser-native CAD kernel built from a Rust geometry engine,
compiled to WebAssembly, and exposed through a TypeScript SDK with Three.js
integration.

It is not a full end-user CAD application. It provides the geometry kernel,
projection/export paths, and wrapper APIs that downstream applications use to
build browser CAD tools, AEC/BIM workflows, configurators, and AI-assisted
design frontends.

## Project Overview

OpenGeometry is aimed at browser-side CAD and geometry-heavy applications that
need deterministic modeling operations in a client runtime.

The current public positioning across the GitHub repository and demo site is:

- browser-native CAD kernel for web apps and AI CAD
- Rust engine compiled to WebAssembly for browser execution
- Three.js-friendly SDK surface for rendering and integration
- strong fit for CAD, AEC/BIM, configurators, and AI-assisted design tools
- geometry execution that stays inside the browser instead of delegating core
  modeling logic to a remote backend

## Core Introduction

OpenGeometry can be described in one sentence as a high-performance browser-side
geometry engine for professional-grade 3D design and CAD-style workflows.

The most useful top-level summary is the following five-point view.

### 1. Technical identity

OpenGeometry is a browser-native CAD kernel.

- it is intended to run core geometry logic in the browser
- it uses Rust for performance-sensitive kernel behavior
- it compiles that kernel to WebAssembly for browser execution
- it provides a Three.js-friendly SDK surface for integration and visualization

### 2. Main capabilities

The current project supports the main categories expected of a browser CAD
kernel:

- primitive and shape creation
- boolean operations
- extrusion and sweep workflows
- projection and export workflows
- geometry handling that goes beyond display-only rendering

### 3. Fit for AI-assisted CAD

OpenGeometry is especially relevant for AI-assisted design tools because the
kernel behavior is explicit and deterministic.

This makes it suitable for systems where an AI agent or copilot suggests or
constructs modeling steps while the kernel executes the actual geometry logic in
a predictable way.

The repository also includes explicit agent guidance through `AGENTS.md`, which
helps align automated development and code generation with the current project
structure.

### 4. Target use cases

The clearest downstream use cases include:

- browser CAD and BIM tools
- AEC-focused modeling workflows
- product configurators with live geometry updates
- AI-driven design and modeling assistants
- geometry-heavy web applications that need exportable model data

### 5. Main differentiator

The key distinction is that OpenGeometry is not just a visualization helper.

It acts as a geometry kernel, meaning it performs actual modeling and topology
work rather than only drawing meshes. This makes it more suitable for workflows
that need reliable geometry computation, exportable results, and CAD-style
behavior inside the browser runtime.

### Demo expectation

The public demos are an important proof point for this positioning because they
show the kernel running interactive geometry operations directly in the browser.

## Capability Summary

The current project surface can be described in five main buckets.

### Primitives

The primitive layer provides foundational sketch and profile elements:

- line
- arc
- curve
- polyline
- rectangle

These are represented in the Rust kernel under `main/opengeometry/src/primitives/`
and surfaced in TypeScript through `main/opengeometry-three/src/primitives/`.

### Shapes

The shape layer provides higher-level planar and volumetric entities:

- polygon
- solid
- cuboid
- cylinder
- sphere
- wedge
- sweep
- opening

These are exposed in TypeScript through `main/opengeometry-three/src/shapes/`
and backed by Rust kernel types in `main/opengeometry/src/primitives/` plus
freeform and operation-level helpers where needed.

### Operations

The current operation surface includes:

- triangulation
- extrusion
- sweep
- offset
- boolean union, intersection, and subtraction
- polygon boolean operations
- wall-from-offset style composition workflows

The Rust-side implementation lives mainly in:

- `main/opengeometry/src/operations/`
- `main/opengeometry/src/booleans/`

The TypeScript-facing wrappers and convenience APIs live mainly in:

- `main/opengeometry-three/src/operations/`
- `main/opengeometry-three/src/examples/`

### Export and projection

The current export and downstream geometry-delivery surface includes:

- STL export
- STEP export
- IFC export
- 2D projection and classified line output
- native PDF projection export

PDF remains native-only in this repository. Browser PDF export is not currently
implemented here.

### Integration

The integration layer currently emphasizes:

- Three.js rendering integration
- wasm initialization and runtime bridging
- scene-level snapshot storage and projection
- freeform editing bridge APIs

## Demo-Site Feature Map

The public demo catalog at `demos.opengeometry.io` currently organizes examples
under three main headings.

### Primitives demos

The demo site currently highlights these primitive examples:

- Arc: circular arc with span and segment controls
- Curve: control-point curve for route and profile sketching
- Line: two-point line primitive with direct endpoint control
- Polyline: open and closed path definitions for profile work
- Rectangle: parametric rectangular primitive for base profiles

### Shapes demos

The demo site currently highlights these shape examples:

- Cuboid: rectangular solid for massing, rooms, and equipment blocks
- Cylinder: cylindrical volume for ducts, pipes, and shafts
- Opening: helper volume for void and penetration previews
- Polygon: planar polygon triangulation for surfaces and slabs
- Sphere: sphere-based clearance or envelope studies
- Sweep: profile-along-path sweep for framing and custom sections
- Wedge: tapered solid for ramps and sloped technical elements

### Operations demos

The demo site currently highlights these operation examples:

- Boolean operations for solid union, intersection, and subtraction
- Polygon boolean operations for coplanar planar workflows
- Offset generation
- STL export workflow
- Sweep path plus profile workflow
- Wall from offsets composite workflow

These demos are useful as practical indicators of the current public SDK
surface and should be treated as product-facing examples of what the repository
already supports well.

## Repository Structure

This repository follows a monorepo-style layout with multiple packages and
supporting directories inside one source tree.

## Directory Structure Guide

The most important directories and their responsibilities are as follows.

### `main/opengeometry`

This is the core engine of the project.

- written in Rust
- owns core geometry and topology behavior
- performs boolean operations, extrusion, offset, sweep, projection, export,
  and editing-related kernel logic
- compiled to WebAssembly for browser use
- also supports some native-only export paths such as the current PDF export

This directory should be treated as the main implementation authority for
geometry behavior.

### `main/opengeometry-three`

This is the primary browser integration layer and the most complete downstream
adapter in the repository today.

- connects the kernel to Three.js
- exposes the public wrapper APIs used by browser developers
- provides renderable shape and operation wrappers
- includes example scenes and example-driven validation paths

For most application developers building on this repository, this is the first
place to study after understanding the core kernel.

### `main/opengeometry-webgl`

This is a future direct-WebGL adapter target.

- intended to support lower-level browser rendering without Three.js
- currently scaffold-only
- not an implemented primary integration target at this time

It should not be treated as production-ready based on the current repository
state.

### `main/opengeometry-babylon`

This is a future Babylon.js adapter target.

- intended for Babylon.js-based integrations
- currently scaffold-only
- not an implemented primary integration target at this time

It should not be treated as production-ready based on the current repository
state.

### `main/opengeometry-ios`

This is a future iOS bindings or platform package target.

- currently scaffold-only
- not part of the active browser SDK path today

### `docs`

This is the documentation source directory.

- used for public developer documentation
- structured around Mintlify configuration and content
- includes conceptual guides and API reference navigation

This directory is for published docs, not internal engineering notes.

### `knowledge`

This directory stores stable project knowledge and architecture notes.

- long-lived engineering reference material belongs here
- this development reference file lives here
- use this directory for durable technical context rather than temporary task
  planning

### `scripts`

This directory contains build and packaging orchestration scripts.

- repository build support lives here
- dist assembly and artifact preparation are driven from here

### `dist`

This is the generated output directory for packaging and distribution.

- contains built artifacts
- not intended for hand editing
- may be regenerated by the build pipeline

### Core modules

- `main/opengeometry/`: Rust kernel, compiled to WebAssembly and native targets.
- `main/opengeometry-three/`: TypeScript and Three.js wrapper layer, examples,
  and higher-level SDK ergonomics.
- `scripts/prepare-dist.mjs`: packaging step that assembles the publishable dist
  bundle from generated wasm artifacts and built TypeScript output.
- `docs/`: user-facing Mintlify documentation source.
- `knowledge/`: stable architecture and project knowledge.

### Supporting directories

- `main/opengeometry-webgl/`, `main/opengeometry-babylon/`,
  `main/opengeometry-ios/`, `main/opengeometry-export-io/`,
  `main/opengeometry-export-schema/`: current scaffolds, not primary runtime
  targets.
- `dist/`: generated publish output, not hand-edited.

## Package Map

The repository currently presents the following package roles at the top level:

- `opengeometry`: Rust core engine compiled to WebAssembly and also used for
  native-only export paths
- `opengeometry-three`: TypeScript and Three.js integration layer intended for
  downstream browser developers
- `opengeometry-webgl`: WebGL-oriented scaffold, not currently a primary
  implemented target
- `opengeometry-babylon`: Babylon.js-oriented scaffold, not currently a primary
  implemented target

Additional directories exist for future or placeholder integration targets, but
the active SDK surface today is driven by `opengeometry` and
`opengeometry-three`.

## License, Documentation, and AI Agent Entry Points

### License

The repository is licensed under MPL-2.0.

### Documentation entry points

The main public references are:

- repository README
- `docs.opengeometry.io`
- demo catalog at `demos.opengeometry.io`
- quickstart and installation guides

### AI agent guidance

`AGENTS.md` is the main repository-level source of truth for architecture,
commands, validation expectations, gotchas, and coding-agent behavior in this
repository.

This matters because future automated or agent-assisted development work should
align with both this reference file and `AGENTS.md`.

## Architecture Summary

### Stack

1. App code runs in the browser or Node.
2. The TypeScript SDK in `main/opengeometry-three/` provides the public wrapper
   layer and Three.js integration.
3. wasm-bindgen glue from `main/opengeometry/pkg/` exposes Rust exports to JS.
4. The Rust kernel in `main/opengeometry/src/` owns geometry generation,
   topology, booleans, projection, export, and direct editing behavior.

### Canonical geometry model

The canonical geometry state is B-Rep, not mesh data.

The main topology model lives in `main/opengeometry/src/brep/` and includes:

- Vertex
- HalfEdge
- Edge
- Loop
- Face
- Wire
- Shell
- Brep

Primitive generation, boolean operations, freeform editing, scene projection,
and export all operate on or derive from this B-Rep representation.

### Mesh generation model

Triangle and outline buffers are derived from B-Rep on demand.

OpenGeometry does not treat triangulated mesh data as the source of truth.
This keeps geometry behavior consistent across:

- rendering
- export
- projection
- freeform editing
- boolean operations

## Runtime Flow

### Initialization

The runtime must be initialized by calling `OpenGeometry.create(...)` before
constructing `Vector3` or kernel-backed wrappers.

This is a hard runtime requirement because `Vector3` is wasm-backed.

### Shape creation flow

1. Application code initializes the wasm runtime.
2. Application constructs wrapper classes such as `Polygon`, `Cuboid`,
   `Cylinder`, `Sphere`, `Wedge`, `Sweep`, or `Opening`.
3. Wrapper classes call Rust-side `set_config(...)` or equivalent generation
   methods.
4. The Rust kernel builds or rebuilds local B-Rep state.
5. Placement transforms are applied when world-space geometry is requested.
6. Triangle or outline buffers are derived and passed back to TypeScript.
7. The Three.js wrapper builds renderable `BufferGeometry` objects.

### Scene and projection flow

1. A world-space B-Rep snapshot is inserted into a scene or registry.
2. Projection runs in Rust against the stored B-Rep snapshots.
3. Results are returned as 2D segment data with classification metadata.
4. Export paths convert those results into STL, STEP, IFC, or native PDF
   outputs depending on the active path.

## Basic Cuboid Example

The following example is the baseline browser-side hello-world workflow for
creating and placing a 3D cuboid with OpenGeometry.

```ts
import { Cuboid, OpenGeometry, Vector3 } from "opengeometry";

await OpenGeometry.create({
  wasmURL: new URL(
    "node_modules/opengeometry/opengeometry_bg.wasm",
    import.meta.url
  ).href,
});

const cuboid = new Cuboid({
  center: new Vector3(0, 0, 0),
  width: 2,
  height: 1,
  depth: 1,
  color: 0x33aa66,
});

cuboid.setPlacement({
  translation: new Vector3(1, 0, 0),
  rotation: new Vector3(0, 0.25, 0),
  scale: new Vector3(1.25, 1.25, 1.25),
});
```

### What each part does

#### Imports

The example imports three main public SDK types:

- `Cuboid`: parametric 3D box shape wrapper
- `OpenGeometry`: runtime initialization entry point
- `Vector3`: wasm-backed 3D coordinate type

#### Runtime initialization

`OpenGeometry.create(...)` loads and initializes the WebAssembly runtime that
backs the geometry kernel.

This step is mandatory before constructing `Vector3` or kernel-backed shape
wrappers.

The `wasmURL` points the browser at the generated `opengeometry_bg.wasm` file,
which contains the compiled Rust kernel logic.

#### Cuboid creation

The `Cuboid` constructor creates a parametric box definition in memory.

In this example:

- the cuboid center is at the origin
- width is `2`
- height is `1`
- depth is `1`
- color is a green tone `0x33aa66`

At this stage, the object exists as a kernel-backed data object ready for use by
the rendering layer.

#### Placement

`setPlacement(...)` applies transform settings to the shape.

In this example it:

- translates the cuboid by `+1` on the X axis
- rotates it around the Y axis
- uniformly scales it by `1.25`

The scale values must remain positive and uniform to satisfy the kernel's
placement rules and preserve expected CAD behavior.

### Practical interpretation

This example should be understood as:

1. initialize the geometry engine
2. create a green cuboid centered at the origin
3. move it slightly, rotate it, and scale it uniformly

After this, the cuboid is ready to be consumed by the Three.js integration
layer, added to a scene, or used in downstream operations.

## Installing The Published Package

The command below installs the published OpenGeometry package into a downstream
application project.

```bash
npm install opengeometry
```

### What the command means

- `npm`: the Node package manager
- `install`: download a package and register it as a dependency
- `opengeometry`: the published package name

### What changes after installation

In a normal JavaScript or TypeScript application, this installation does the
following:

- adds the `opengeometry` package under `node_modules/`
- makes the package importable from application code
- records the dependency in `package.json`
- allows other developers or CI environments to reinstall the same package from
  project metadata

The installed package includes the JavaScript and TypeScript-facing SDK surface
plus the generated WebAssembly asset used by the runtime.

### Why installation matters

Installing the package gives downstream projects access to the kernel-backed API
surface instead of having to implement low-level geometry behavior themselves.

This is the entry point for using higher-level capabilities such as:

- creating primitives and shapes
- extrusion and sweep workflows
- boolean operations
- projection and export workflows

### Important follow-up step

Package installation is not sufficient by itself.

Because OpenGeometry relies on a WebAssembly runtime, application code must also
initialize the engine and provide a reachable wasm asset URL.

Example:

```ts
await OpenGeometry.create({
  wasmURL: new URL(
    "node_modules/opengeometry/opengeometry_bg.wasm",
    import.meta.url
  ).href,
});
```

In practical terms, `npm install opengeometry` is the package acquisition step,
while `OpenGeometry.create(...)` is the runtime activation step.

Both are required for actual browser-side use.

## Starter App Template

A runnable minimal starter page now exists in the example catalog at:

- `main/opengeometry-three/examples-vite/shapes/starter-cuboid-app.html`

This template is the smallest practical browser app shape for downstream work in
this repository.

It demonstrates the baseline stack in one file:

- page-level HTML entrypoint
- shared example styling via `theme.css`
- Three.js scene, camera, renderer, light, and controls setup
- wasm asset import
- `OpenGeometry.create(...)` initialization
- `Cuboid` construction and placement
- animation loop and resize handling

This file should be the first copy-and-modify starting point when spinning up a
small browser prototype from the current example system.

Additional minimal starters now exist for the next most common flows:

- `main/opengeometry-three/examples-vite/shapes/starter-polygon-app.html`
- `main/opengeometry-three/examples-vite/operations/starter-boolean-app.html`
- `main/opengeometry-three/examples-vite/operations/starter-freeform-editor.html`

These split the common first steps into three focused categories:

- polygon profile setup
- solid boolean setup
- freeform face editing setup

## Example Architecture Pattern

The example pages in `main/opengeometry-three/examples-vite/` follow a recurring
pattern that is useful for application development.

### Page structure

Each example page typically contains:

- a simple HTML shell
- a floating title and control panel
- a module script that owns all runtime setup
- an `#app` container for the WebGL canvas

### Runtime structure

The module script typically does the following in order:

1. imports shared styles and runtime dependencies
2. creates a Three.js scene, camera, renderer, controls, grid, and lights
3. initializes OpenGeometry with the wasm asset URL
4. creates one or more kernel-backed wrapper objects
5. binds UI controls to wrapper config or placement changes
6. starts a render loop

### State ownership pattern

The examples separate responsibilities in a simple and reusable way:

- page code owns UI state and event wiring
- OpenGeometry wrapper objects own shape state and kernel interaction
- Three.js owns rendering and camera interaction

This is the cleanest model to follow when building a small application on top of
the SDK.

## Example Analysis: Parametric and Freeform

The most informative advanced example for understanding the SDK flow is:

- `main/opengeometry-three/examples-vite/operations/editor-modes.html`

Its supporting reusable logic is mirrored by:

- `main/opengeometry-three/src/examples/editor-modes.ts`

### What it demonstrates

This example shows the intended progression from parametric editing to direct
freeform editing.

The runtime flow is:

1. create a normal parametric `Cuboid`
2. edit it through standard config and placement APIs
3. convert it to a freeform copy with `toFreeform(...)`
4. create a freeform editor with `createFreeformEditor(...)`
5. identify the top face from topology data
6. push-pull that face on the freeform copy
7. rebuild a visual preview of the edited result

### Why this example matters

This page explains the intended product boundary inside the SDK:

- parametric wrappers are for config-driven editing
- freeform objects are for topology-aware direct editing
- explicit conversion is the bridge between those two modes

That distinction is central to how the SDK is structured and should guide future
application design.

## Important Invariants

These are the main guardrails that should shape future changes.

### Initialization invariant

- Always initialize with `OpenGeometry.create(...)` before constructing
  `Vector3` or wrapper classes.

### B-Rep invariant

- B-Rep is the authoritative geometry representation.
- Do not introduce a parallel canonical mesh representation.
- Triangulation should remain a derived view, not the primary data model.

### Placement invariant

- `Placement3D` is separate from local B-Rep generation.
- Placement scale must remain finite, positive, and uniform.
- Non-uniform or mirrored placement transforms are intentionally rejected.

### Scene invariant

- Scene insertion is snapshot-based.
- Adding an entity to `OGSceneManager` captures B-Rep state at insertion time.
- Later wrapper edits do not auto-propagate.
- Callers must explicitly refresh or replace scene entities after geometry
  changes.

### Generated code invariant

- `main/opengeometry/pkg/` is generated by wasm-pack.
- Do not hand-edit generated pkg files.
- Change Rust source and rebuild instead.

### Triangulation invariant

- `earcutr` is intentionally pinned to `=0.3.0`.
- Do not upgrade it casually; triangulation behavior is treated as sensitive.

### Export invariant

- STL, STEP, and IFC export are supported kernel-level paths.
- PDF export is native-only in this repository and is not currently a browser
  feature.

### Validation invariant

- Rust changes should be validated with Rust formatting, checking, tests, and
  build gates.
- TypeScript changes are not covered by `npm test`.
- TypeScript validation depends on lint, build success, and example-level
  verification.

## What This Repository Is Well Suited For

OpenGeometry is a strong fit for the following browser-side products:

- parametric configurators driven by numeric inputs
- browser-based CAD tools with deterministic geometry behavior
- AEC/BIM-style tools using walls, openings, extrusion, subtraction, and
  projection workflows
- AI-assisted design tools that translate user intent into explicit kernel
  operations
- geometry-heavy web tools needing export or projection capabilities

## What Still Belongs To The Application Layer

This repository does not, by itself, provide a complete end-user modeling app.

Applications built on top of it still need to provide their own:

- UI and interaction layer
- selection, snapping, and editing UX
- history and undo/redo models
- project/file management UX
- collaboration or persistence infrastructure
- generalized import workflows

## Recommended Development Direction

When extending this repository or using it as a base for a browser CAD tool,
prefer the following model:

1. Keep geometry logic in the Rust kernel whenever the behavior changes B-Rep,
   projection, export, or edit semantics.
2. Keep TypeScript focused on ergonomics, visualization, wrapper composition,
   and user-facing integration.
3. Reuse the freeform editing layer for direct topology manipulation instead of
   embedding editing logic into every shape wrapper.
4. Treat `OGEntityRegistry` as the newer export/projection-oriented direction
   while remaining compatible with `OGSceneManager` during transition.

## Build And Validation Notes

## Session Record: 2026-05-04

The work completed in this session followed the sequence below.

### 1. Repository intake and architecture scan

- scanned the repository structure and core package boundaries
- confirmed that `main/opengeometry/` is the Rust kernel authority
- confirmed that `main/opengeometry-three/` is the active browser integration layer
- mapped the main runtime pattern from wasm initialization through wrapper creation and Three.js rendering

### 2. Durable reference creation

- created this file as the long-lived engineering reference for the repository
- added product definition, architecture summary, constraints, build flow, and validation expectations
- added update-log discipline so future work can be appended instead of recreated

### 3. Environment bring-up on Windows

- identified that the local environment was missing Rust toolchain components required by the build flow
- installed the Rust toolchain support and `wasm-pack`
- verified the working local tool versions after setup

### 4. Build and runtime verification

- ran `npm install`
- ran `npm run build-core`
- ran `npm run build`
- ran `npm test`
- started the Vite example server from `main/opengeometry-three`
- opened the example catalog and verified the Cuboid Placement page in the browser

### 5. Example analysis and starter extraction

- analyzed the existing shape and operation example pages
- extracted a minimal Cuboid starter page as the first downstream template
- analyzed the parametric-to-freeform flow in the editor example
- extracted additional minimal starters for polygon, boolean subtraction, and freeform push-pull

### 6. Catalog and documentation updates

- linked the new starter pages into the example catalog
- updated `knowledge/README.md` to point to this reference file
- recorded the verified environment, workflow, example architecture pattern, and starter inventory here

### 7. Observed limitations and notes

- the Tally dashboard inspection attempt was blocked by authentication and could not be completed from an unauthenticated page load
- `package-lock.json` changed as a result of local `npm install` state and was left intact rather than being reverted
- TypeScript validation remains build-plus-example based because repository tests still cover Rust only

## Verified Local Environment Snapshot

The following local environment state was verified on 2026-05-04 while building
and testing this repository on Windows.

- Node.js: `v24.15.0`
- npm: `11.12.1`
- cargo: `1.95.0`
- rustc: `1.95.0`
- rustup: `1.29.0`
- wasm-pack: `0.14.0`

This snapshot reflects a working local setup for the repository's default build,
example-run, and test flow.

## Verified Bring-Up Checklist

The following sequence has been executed successfully in this workspace.

1. Install Rust toolchain support and `wasm-pack`
2. Run `npm install`
3. Run `npm run build-core`
4. Run `npm run build`
5. Run `npm --prefix main/opengeometry-three run dev-example-three`
6. Open the example catalog at `http://localhost:5173/`
7. Open the Cuboid Placement example at
   `http://localhost:5173/shapes/cuboid-placement.html`
8. Run `npm test`

## Verified Results

The following outcomes were confirmed during local validation.

### Build results

- `npm run build-core`: succeeded
- `npm run build`: succeeded

### Example app results

- the Vite example server started successfully on `http://localhost:5173/`
- the example catalog page rendered successfully
- the Cuboid Placement example page rendered successfully
- placement controls were interactive; updating `Translate X` changed the UI
  value state as expected

### Test results

- `npm test`: succeeded
- Rust unit tests: `135 passed`
- Rust integration smoke tests: `4 passed`
- no test failures were observed
- the `cargo test --examples` warning remained a known benign no-op in this
  repository configuration

## Local Build And Run Workflow

This is the practical command flow for developers working with the repository on
their own machine.

### Prerequisites

Local development requires the following tools to be installed first:

- Node.js and npm
- Rust toolchain
- `wasm32-unknown-unknown` Rust target
- `wasm-pack`

Without `wasm-pack`, the Rust-to-WASM build path will fail before the SDK can
be rebuilt.

### Command sequence

The standard local workflow is:

1. `npm install`
2. `npm run build-core`
3. `npm run build`
4. `npm --prefix main/opengeometry-three run dev-example-three`
5. `npm test`

### What each command does

#### `npm install`

Installs JavaScript and TypeScript dependencies used by the repository.

Use this first when setting up the project locally or after pulling dependency
changes.

#### `npm run build-core`

Builds the Rust kernel and converts it into WebAssembly using `wasm-pack`.

This is the command that produces the generated wasm-bindgen output consumed by
the TypeScript wrapper layer.

It is the key step that turns the kernel into something the browser runtime can
actually load.

#### `npm run build`

Runs the full repository build pipeline.

This includes:

- Rust core build
- TypeScript wrapper bundling
- dist packaging and wasm asset preparation

Use this when you want the full publishable or integration-ready output rather
than only the kernel build.

#### `npm --prefix main/opengeometry-three run dev-example-three`

Starts the Three.js example application from the SDK package.

This is the most direct way to validate that the local browser-side integration
is working and to inspect example geometry behavior in a running app.

In a normal setup, this serves a local Vite development site and exposes the
examples in the browser.

#### `npm test`

Runs the repository test command.

In this repository, this primarily covers Rust unit and integration tests.
It does not provide comprehensive TypeScript unit-test coverage.

### Practical interpretation

In development terms, this command sequence is the basic local bring-up path for
the repository:

- install dependencies
- build the Rust kernel into WASM
- assemble the full SDK build
- run the example application
- validate the kernel through tests

This should be treated as the default first-run workflow for anyone modifying or
evaluating the codebase locally.

### Build order

The build pipeline is order-sensitive:

1. `npm run build-core`
2. `npm run build-three`
3. `npm run prepare-dist`

`npm run build` runs these in the correct order.

### Validation expectations

- Docs-only changes: `npm run build` as a sanity check when feasible.
- Rust changes: `cargo fmt --check`, `cargo check`, `cargo test`, `npm run build`.
- TypeScript changes: `npm run lint:check`, `npm run build`, and manual example
  validation.

## Update Protocol

When a meaningful change lands, append a new dated entry to the log below.

Each entry should capture:

- what changed
- why it matters
- affected areas
- new constraints or removed constraints
- validation that was run, if relevant

Use concise entries. This file is meant to be actively maintained, not turned
into a long narrative document.

## Update Log

### 2026-05-03

- Created this reference document to capture the current verified architecture,
  runtime constraints, extension direction, and development guardrails.
- Recorded the most important invariants: wasm initialization ordering,
  B-Rep-first design, snapshot-based scene behavior, placement restrictions,
  generated pkg discipline, native-only PDF export, and the current validation
  model.
- Established this file as the default long-lived reference for future updates
  when architecture or workflow assumptions change.

### 2026-05-03

- Expanded the reference with a source-backed project overview based on the
  repository README, package structure, and public demo catalog.
- Added capability breakdowns for primitives, shapes, operations, export, and
  integration so future work can stay aligned with the publicly demonstrated
  surface area.
- Recorded the current demo-site feature map, package roles, MPL-2.0 license,
  and AI-agent entry point expectations centered on `AGENTS.md`.

### 2026-05-03

- Added a dedicated local build-and-run workflow section covering prerequisites,
  command order, and the practical meaning of the main terminal commands used by
  developers.
- Recorded that `npm install`, `npm run build-core`, `npm run build`, example
  launch, and `npm test` together form the standard local bring-up path for the
  repository.
- Documented that `wasm-pack` is a required prerequisite for successful local
  WASM builds in this environment.

### 2026-05-03

- Added a monorepo-oriented directory structure guide describing the role of the
  main packages and support folders in the repository.
- Clarified that `main/opengeometry-three` is the most complete integration
  layer today, while `main/opengeometry-webgl`, `main/opengeometry-babylon`, and
  `main/opengeometry-ios` are currently scaffold targets rather than mature
  runtime surfaces.
- Recorded `docs/` as the Mintlify-based public documentation source and
  `knowledge/` as the durable engineering-reference area for future work.

### 2026-05-03

- Added a basic `Cuboid` quick-start example showing runtime initialization,
  cuboid construction, and placement in the browser-facing SDK.
- Documented the role of `Cuboid`, `OpenGeometry`, and `Vector3`, plus the
  requirement that wasm initialization must complete before using kernel-backed
  runtime types.
- Recorded the practical meaning of the example as the baseline hello-world path
  for creating a 3D object ready for scene integration.

### 2026-05-03

- Added a downstream package-installation section for `npm install opengeometry`
  describing what the command does and how it affects an application project.
- Recorded that installing the package only acquires the SDK and wasm asset;
  runtime use still requires explicit `OpenGeometry.create(...)`
  initialization.
- Clarified the difference between package installation and wasm runtime
  activation for future onboarding and documentation work.

### 2026-05-03

- Added a concise five-point product introduction covering technical identity,
  main capabilities, AI relevance, target use cases, and the core differentiator
  between OpenGeometry and visualization-only web 3D tooling.
- Recorded the public demos as a practical confirmation of the project's
  browser-native kernel positioning.

### 2026-05-04

- Recorded a verified Windows local-environment snapshot after installing the
  missing Rust toolchain components and `wasm-pack`.
- Added a bring-up checklist capturing the exact default execution order that was
  run successfully in this workspace.
- Logged successful results for `npm install`, `npm run build-core`,
  `npm run build`, local example serving, Cuboid Placement example loading, and
  `npm test`.

### 2026-05-04

- Added a runnable starter example page at
  `main/opengeometry-three/examples-vite/shapes/starter-cuboid-app.html` and
  linked it from the example catalog.
- Documented the shared example architecture pattern so future work can follow
  the same page-owned-UI plus wrapper-owned-geometry structure.
- Added a focused analysis of the Parametric and Freeform example as the clearest
  reference for the SDK's parametric-to-freeform editing flow.

### 2026-05-04

- Added three more minimal starter pages for polygon, boolean subtraction, and
  freeform push-pull workflows so common downstream app entry points now have
  copyable examples.
- Linked the new starters from the example catalog so they are discoverable next
  to the fuller demos.

### 2026-05-04

- Added a session-level execution record summarizing repository intake,
  environment bring-up, validation steps, starter extraction, and the known
  constraints observed during this work.