# Architecture

ALB has a small number of major runtime pieces.

## Main modules

- `AlbPlugIn`: EuroScope entry point, timers, state orchestration, policy helpers, and backend seqsync mode or queue handling
- controller and model objects: maintain active timelines and derived planning state
- `AlbTimelineView`: renders the timeline, stats block, labels, and hit targets
- `AlbIntercom`: collaboration, peer presence, and FMR-related transport behavior
- config parsing: timeline, layout, airport, and runtime option loading

## Loader and core split

The shipped runtime is intentionally split:

- `ALB.dll` is the stable EuroScope-facing loader
- `ALBCore.dll` contains the real ALB runtime

This matters to both packaging and documentation:

- EuroScope loads `ALB.dll`
- release notes and install docs should describe the loader/core model
- config guidance should assume the live user file is `alb-config.json`, not a bundled default

## High-level data flow

At a high level, ALB does four things repeatedly:

1. Collect relevant flight data from EuroScope
2. Build or update timeline and statistics state
3. Apply sequencing and planning rules
4. Render the result and synchronize shared state with peers when needed

## Backend seqsync layer

Within that flow, backend sequence synchronization is the transport layer that
ships canonical per-aircraft sequence state from the authoritative FMR to peer
ALB instances.

The important architectural split is:

- sequencing logic decides the local canonical state
- backend seqsync decides whether that canonical state is sent immediately, queued, horizon-suppressed, or suspended
- peer apply logic mirrors canonical backend state without becoming a second independent sequencing engine

In this context, canonical means the official shared sequence truth from the
FMR. On peers, that canonical state is the authoritative source for order, EAT,
PLT, timeline anchoring, sequence influence, and special treatment state.

Peers may still compute local live or presentational data around that canonical
state, such as:

- flight phase and local visibility
- live distance or countdown formatting
- local warnings and OBS surfaces
- tag decoration and display hydration

## UI-to-model connection

The public operator controls map into runtime behavior roughly like this:

- top buttons write local display state or shared planning policy
- stats-area clicks adjust PLR or per-via-fix spacing
- aircraft right-click actions trigger explicit resequencing helpers
- menus switch active timelines, filters, peers view, layouts, and a retained legacy scenarios surface

That is why the operator docs are organized around the window itself, while the technical docs are organized around authority, sequencing, and transport.
