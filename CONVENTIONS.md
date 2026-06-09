# Conventions

This is the design contract the system runs on. It is deliberately generic: a
reader should be able to lift these conventions into their own vault without
needing anything specific to mine. Everything below is enforced either by note
templates, by skills that read and write the vault, or by the hygiene scripts
that scan for drift.

## Two structures at right angles

The vault is organized by **PARA** (how knowledge rests). The flow that fills
and acts on it is the **4-layer model** (how information moves). PARA tells you
where a note lives; the 4-layer model tells you how it got there and what acts
on it. Keep the two separate in your head and the rest of the conventions fall
out cleanly.

## `note_type` enum

Every note carries a `note_type` from a small, locked enum. The type drives how
templates render it, how skills route it, and which hygiene rules apply.

| `note_type` | What it is | Lives in (PARA) |
|---|---|---|
| `source` | Captured raw material: a clipped article, a transcript, a saved link | Inbox, then Resources or Archive |
| `resource` | A durable single-idea note, written in your own words | Resources |
| `area` | A life-area hub that indexes a domain and links its resources | Areas |
| `project` | An active, multi-step initiative with a status and next actions | Projects |
| `state` | Live system state: current focus, active projects, decisions log | meta-folder |
| `brief` | A generated daily activation brief | meta-folder |
| `synthesis` | A generated weekly or monthly rollup | meta-folder |

The enum is **closed on purpose**. Adding a value is a schema change: you update
the canonical schema doc and teach the hygiene scanner the new value in the same
commit, or every note that uses it gets flagged as malformed. That friction is a
feature. It keeps the type system from sprawling.

## Frontmatter shape

Every note opens with YAML frontmatter. The shared keys:

```yaml
---
created: 2026-01-15
updated: 2026-01-15
note_type: resource
tags:
  - coffee
  - brewing
---
```

Captured notes (anything that enters through Layer 1) add a `captured_from`
discriminator so you always know how a thing arrived:

```yaml
captured_from: web-clipper   # web-clipper | telegram | raindrop | manual
processed: false             # flips to true once triaged
```

`captured_from` is a closed enum, same discipline as `note_type`. It is the
difference between "I wrote this" and "this arrived from somewhere," which
matters when you are deciding what to trust and what to triage.

**Render rule:** no blank line between the closing `---` and the first heading.
A gap there renders as dead space in the properties panel.

## PARA mapping

| PARA category | Folder | Holds |
|---|---|---|
| Projects | `Projects/` | Active multi-step initiatives |
| Areas | `Areas/` | Life-area hubs (Coffee, Training, ...) |
| Resources | `Resources/` | Durable single-idea notes, grouped by area |
| Archives | `Archive/` | Retired projects, sources, and system notes |

PARA is a knowledge taxonomy. It has no slot for live state, tooling, or capture
staging, so the vault adds four `_`-prefixed meta-folders that PARA does not
define:

| Meta-folder | Holds |
|---|---|
| `_Dashboard/` | Live state: current context, daily briefs, syntheses, decisions log, hygiene reports |
| `_System/` | Tooling and schema: tool notes, tag taxonomy, frontmatter schema, the architecture map |
| `_Templates/` | Note templates, one per `note_type` |
| `Inbox/` | Capture staging (Layer 1) |

The `_` prefix is **reserved for top-level system folders** and never appears
inside a content folder. That single rule keeps "the system's own machinery"
visually and structurally separate from "the knowledge the system holds."

## Capture to triage flow

`Inbox/` is a staging area, not a destination. Nothing is allowed to rot there.
A triage pass walks the inbox and gives each item:

1. A **system-fit scan** — does this belong in the vault at all, and if so where?
2. A **verdict** from a closed set:

   | Verdict | Meaning |
   |---|---|
   | `act` | Turn it into a project or a task now |
   | `save` | Promote it to a durable resource note |
   | `backlog` | Defer it to a tracked backlog, not the inbox |
   | `ignore` | Drop it; it was noise |
   | `hold` | Keep it staged with a reason; revisit next pass |

Triage runs in **bounded batches**. A pass stops after a small number of
actionable verdicts and hands off to a fresh session rather than grinding a
500-item inbox in one sitting. Bounded batches keep each pass cheap and keep
attention from collapsing halfway through a long queue.

## Backlinks and hubs

A new `resource` note auto-appends a backlink to its life-area hub, so an `area`
note stays a live index of its domain without manual upkeep. The hub is the
entry point; the resources hang off it. This is the one piece of write-time
automation in the convention set, and it is what keeps areas from going stale.

## Why the enums are closed

Source, resource, area, project, state, brief, synthesis. Web-clipper, telegram,
raindrop, manual. Act, save, backlog, ignore, hold. Three small closed sets do
most of the structural work in the system. Closed enums are what let a
deterministic script tell "healthy" from "drifted" without an LLM in the loop —
the hygiene scanners described in the README lean entirely on this discipline.
Open-ended metadata cannot be checked; closed enums can.
