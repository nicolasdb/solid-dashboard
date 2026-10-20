# Slices

Each slice is usable on its own and is tried for real before the next one
starts.

## Where we are (20 Oct 2026)

Design debated and written ([design](explanation/design.md)). One hot
capture of a Claude Code session was written by hand through the connector
and reviewed; what broke or rubbed:

- after a compaction, the earlier part of the session survives only as a
  summary: no verbatim, no times;
- `raw` is verbatim only for the person's words; the agent's own lines are
  already paraphrases;
- a proposal nobody contradicted is not a decision;
- an integer timestamp is required where the time is only approximate;
- item slugs invented on the spot differ from the node ids in the
  hand-written view: one item registry is needed;
- dependencies exist twice (the view's arrows and the traces) until the
  view is generated;
- the agent chose what deserved a trace, not the person.

It also found a real problem by checking an item marked "verify tomorrow":
the loop from view to action works.

## 0 · Claude capture · next

On the fly: the skill writes a trace through the connector during a
session. Then a Claude Code harvest adapter, run by `SessionEnd` and
`PreCompact` hooks, turning the session log into raw traces. Packaged as a
Claude Code plugin.

## 1 · Session end → trace → view

The agent proposes traces at the end of a session, the person validates a
plain-language diff, the overview is regenerated (two projects, Mermaid).
Needs the schema extension.

## 2 · The brief

At session start: actionable now, +1, +2, fog, for one project or all.

## 3 · Closing with propagation

Evidence, archive, "closing X unblocks Y".

## 4 · Stale and parked

## 5 · External capture

Pasted text from other chats or meeting notes.

## 6 · Outputs

A distilled report to a collective; notes towards a publication.

## Later

The desktop app (index, Cytoscape, other views), source-map checks.
