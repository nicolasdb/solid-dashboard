# Design

Why the dashboard works the way it does. Decided in a design debate on
2026-10-20; nothing here is built yet.

## Intent

Someone working on shared **horizontal** layers (pods, identity, a
connector, servers, design systems) and on the **verticals** that use them
(one collective or domain each) has to hold a large map in their head:
what is done, what blocks what, what is not even scoped yet. The dashboard
holds that map instead.

The view is a **fog of war**: what is actionable **now**, at **+1**, at
**+2**, and what is still **unknown**. Its main use is reading ("where am
I, what can I start"); writing exists only to feed it.

## Principles

- **User-centred, not repo-centred.** The single source of truth is the
  person's own pod. Their projects and their sessions with agents inform
  it; distilled information is then reported out to each collective.
  Collectives may be separate organisations with their own repos, so
  issue trackers are not the input. An issue can be linked to an item by
  hand, never synced.
- **Conscious documentation: nothing automatic, but automated.** The agent
  proposes, the person validates.
- **Validate at the exit, not at the entry.** Everything captured is kept:
  the raw trace is the evidence. The person validates a change to the view
  as a plain-language diff ("X done, so Y and Z become actionable"), and
  what leaves for a collective or a publication. They never have to read
  Turtle or Mermaid; code checks the form.
- **One store, several views.**
- **Traces are decoupled from evaluation**, so that knowing they may be
  analysed later does not change what gets captured.
- **The tool must serve its user**, at the start of a session above all.
  A process the person stops feeding when their focus moves elsewhere was
  not serving them enough. Pausing is normal: projects stop because a
  dependency is missing, or because they were side quests, small settings
  to answer an uncertainty. The dashboard records why an item is parked,
  it does not nag.
- **Built for transversal attention.** Its user jumps between subjects,
  goes deep on each for a while, and needs to switch to stay attentive. The
  map is what lets them come back to a subject weeks later without
  rebuilding it from memory.

## The store: traces

Traces live in the person's pod, in the capture pipeline:

```
sources → raw → gists → validation → graph index → views
```

- **Raw**: what was said or done, never rewritten.
- **Gists**: a distilled observation pointing back to its raw traces.
- An **item** (something with a dependency or a decision) has a stable
  slug; its state is its latest validated trace. A trace carries a status
  and the items it depends on; everything else is free text.
- Fine-grained trace types (pivot, attempt, decision, learning, don't
  know) stay in the capture layer: they keep the decision path, including
  failures, which a final result hides.

Fields: [capture schema](../reference/capture-schema.md).

**Source of a trace**: platform + project + context + pointer (a session
id, a URL, a transcript). Not "who": it is always the person, through some
agent.

## Capture by surface

| Surface | On the fly | Harvest |
|---|---|---|
| Claude Code | skill + a `/note` command, writing through the Solid MCP connector | hooks `SessionEnd` and `PreCompact` run an adapter on the session log |
| claude.ai | the same connector + a skill | no hooks: the account's data export, read on demand |
| Hermes (Matrix, Discord) | its own tools | an adapter reading Hermes's session store |
| Other chats, meetings | pasted text | — |

A hook is a shell process and cannot use the MCP connector: it either
writes locally for the skill to send later, or holds its own agent
credential with only the right to append raw traces.

`PreCompact` matters: once a session is compacted, what came before exists
only as a summary.

## Views

- Depth is **computed** from dependencies: now = no open prerequisite;
  then +1, +2; unknown = items marked unscoped, written as dated questions.
- **Mermaid** is an export of the index, readable anywhere Markdown is.
- The app draws the same index with Cytoscape.js (hierarchical layout,
  depth filter, click through to the source). Other views can come from
  the same index: rings (now at the centre, fog outside), a forest
  (verticals as canopy, deployable code as understory, horizontals as
  substrate, principles as soil).

## Moments of use

1. **Session start**: a brief for the project.
2. **Session end**: the agent proposes traces, the person validates.
3. **Ceremony**: closing an item is like merging a branch: evidence,
   archive, and propagation (closing X unblocks Y). Stale items are
   flagged, never deleted automatically; parked items are not stale.

## Outputs: the membrane

Nothing leaves the pod unless the person chooses it:

- a distilled report to a collective;
- notes towards a publication (an article, a portfolio entry).

**Ready to write?** Traces compound in the personal graph, so the
dashboard can tell when a subject holds enough material for an article:
below a minimum the post would be thin, above a maximum it would be
indigestible and should be split. Volume alone is a poor signal; the shape
of the material counts more. A subject is ready when it holds a story: the
problem, attempts that failed, a pivot, a result, and what was learned.
Thresholds are to be tuned on real posts.

## Source map

Files live in many places: pods, git repos, local folders, sync services.
A source map lists them (type, location, visibility, backup). A moved
document must be surfaced, never break the index silently. Options, to be
chosen: a periodic check of every pointer; on a miss, search by hash or
title and propose the new location; the tool that moves a file writes a
trace of the move; git rename detection.

## Graph and app

- No graph store is needed for the first slices: a script reads the
  traces and computes depth in memory.
- The index is **disposable and rebuildable**; the pods stay the truth.
- Target: a **local desktop app**, not a web app (for instance Tauri, which
  can embed Oxigraph as a Rust library). It rebuilds its index from the
  person's pod and their collectives' pods with the person's own
  credentials, so it only holds what they may read. A shared triple store
  would not isolate anyone: named graphs do no access control.
- Offline: local-first, append-only traces with one writer per file, so
  merging stays simple.

**Single source of what.** There is more than one source of truth, each
for a different thing, and that is not a contradiction. The **pod** is the
source of truth for what must stay online and shared: the identity
(WebID, login), application preferences, the exchange zones with
collectives (their ACLs), and the transversal information: traces, the
index of where things live (repos, local files), and the navigation across
projects and collectives. The **local app** is the source of truth for its
working copy while offline, and syncs with the pod. Which kind of data has
which source, who writes it and who only reads it is a mapping to write
down (RACI-like), not one choice.

## Open questions

- Swipe validation per gist vs confrontation to a collective's principles:
  complementary or one replacing the other.
- Exact values for an item's status, and a "proposed" state distinct from
  "decided".
- A field telling whether a trace's text is the person's own words or an
  agent's paraphrase.
- The source-of-truth mapping per kind of data (pod, local app, repos).
- Reasons for a pause as item data (missing dependency, side quest).
- Article-readiness thresholds.
- Source map checks: which, how often.
- Visibility per item for reports.
