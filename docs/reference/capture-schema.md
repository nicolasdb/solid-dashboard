# Capture schema

TOML, two levels, one protocol. Version 2 (30 Aug 2026) is in use by the
Hermes pipeline; the **v2.x extension** below is a proposal, tried by hand
once (20 Oct 2026).

> The working copy of v2 still lives with the Hermes pipeline (in French).
> This page is meant to become the reference; until then, a change made
> there must be carried here.

| Level | Tables | Content | Where (in the pod) |
|---|---|---|---|
| Raw | `[session_capture]` + `[[trace]]` | messages as they were, unfiltered | `capture/raw/` |
| Gist | `[gist_session]` + `[[gist]]` | a distilled observation, 3–5 sentences | `capture/gists/` |

Both carry `version_schema = "v2"`. A gist comes from one or more traces;
its header points to them.

## Raw

### `[session_capture]`

| Field | Type | Description |
|---|---|---|
| `version_schema` | string | `"v2"` |
| `timestamp_capture` | int | Unix time of the extraction |
| `profile` | string | Source profile (a Hermes profile, or `claude-code`) |
| `session_id` | string | Session id, for tracing back |
| `traces_count` | int | Number of traces in the file |

### `[[trace]]`

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | yes | `trace-{profile}-{session_prefix}-{seq}` |
| `timestamp` | int | yes | Unix time of the original message |
| `type` | string | yes | Trace type |
| `raw` | string | yes | Original text, never reworded |
| `raw_type` | string | recommended | `narrative` / `analyse` / `meta` / `decision` |
| `source` | string | recommended | `session:{profile}/{session_id}` |
| `status` | string | recommended | `provisoire` (default) / `valide` / `archive` |
| `confidence` | float | recommended | 0.0–1.0, default 0.5 |
| `tags` | array | optional | Free tags |
| `routing` | string | optional | `automate` / `humain` / `intermediaire` |
| `capture_point` | string | optional | Protocol phase that produced it |
| `parent_id` | string | optional | Parent trace, `"null"` if none |
| `message_id` | int | optional | Message id in the source store |

## Gist

### `[gist_session]`

| Field | Type | Description |
|---|---|---|
| `version_schema` | string | `"v2"` |
| `timestamp_distillation` | int | Unix time the gists were made |
| `timestamp_capture` | int | Unix time of the source capture |
| `profile` | string | The distiller's profile |
| `archive` | string | Source trace file(s) |
| `traces_source` | array | Raw files used |
| `traces_raw_count` | int | Raw traces in |
| `gists_count` | int | Gists out |
| `ingested` | bool | Set to `true` once loaded into the graph |

### `[[gist]]`

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | yes | Unique id |
| `timestamp` | int | yes | Original Unix time |
| `type` | string | yes | Seen in use: `protocole`, `apprentissage`, `decision` |
| `titre` | string | yes | Short title |
| `validation` | string | yes | `pending` / `validated` / `rejected` |
| `raw` | string | yes | The observation, 3–5 sentences |
| `source`, `raw_type`, `status`, `confidence`, `tags`, `routing`, `parent_id` | | recommended / optional | As in raw |
| `note` | string | optional | The person's note |
| `triage_flags` | array | optional | `revisit`, `priority`, `anagnorisis` |

## v2.x proposal: fields for the dashboard

Added to `[[trace]]` (and carried to `[[gist]]`), all optional:

| Field | Type | Description |
|---|---|---|
| `project` | string | Project or vertical, for per-project views |
| `platform` | string | e.g. `claude-code/cli`, `claude.ai`, `matrix`, `discord` |
| `mode` | string | `capté` (captured live) / `moissonné` (harvested) / `moissonné-résumé` (harvested from a summary) |
| `item` | string | Stable slug of the item the trace is about |
| `item_status` | string | `open` / `blocked` / `done` / `parked` / `unscoped` |
| `depends_on` | array | Slugs of the items it waits for |

Seen missing in the first try, not yet proposed:

- who wrote the text (the person, or an agent's paraphrase);
- a `proposed` state, distinct from a decision;
- an approximate timestamp;
- one registry of item slugs.
