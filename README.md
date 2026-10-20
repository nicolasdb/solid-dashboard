# solid-dashboard

A personal dashboard for people who work across several projects and
collectives. It keeps the map of what is going on (what can start now,
what waits for what, what is still unknown) out of your head and in your
own Solid pod, fed by traces from your working sessions with AI agents.

Status: design only, nothing built yet. See [where we are](docs/slices.md).

## What is in this repo

One product, installed in several places:

- **the skill** an agent (Claude, Hermes…) uses to read the dashboard and
  propose traces;
- **source adapters** that turn session logs into raw traces (one per
  platform, installed where that platform runs);
- **the capture schema** every adapter and reader shares;
- later, **the desktop app** that rebuilds a graph index from your pods
  and draws it.

## Documentation

Organised with [Diátaxis](https://diataxis.fr/):

| | |
|---|---|
| Tutorials | none yet |
| How-to guides | none yet |
| Reference | [Capture schema](docs/reference/capture-schema.md) |
| Explanation | [Design](docs/explanation/design.md) |
| Progress | [Slices, where we are](docs/slices.md) |

## Licence

[Apache 2.0](LICENSE).
