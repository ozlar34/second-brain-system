---
note_type: project
created: 2026-01-08
updated: 2026-01-15
status: active
tags:
  - home-server
  - automation
---
# Home Server

A small always-on machine on the home network that hosts the automation layer:
a workflow engine, a local database, and a transcription service. It exists so
that scheduled and event-driven jobs run without the laptop being awake.

## Status

**Active.** Core services are up and reachable on the private network. Working
through hardening and a couple of flaky scheduled jobs.

## Why it exists

The laptop is not always on, and unattended automation cannot depend on it. The
server takes the always-on half of the workload so the laptop only handles the
interactive, on-demand half.

## Next actions

- [ ] Add a health check to the nightly job so a silent failure pages me.
- [ ] Move the transcription service behind the same restart policy as the rest.
- [ ] Document the restore steps for the database in this note.

## Open questions

- Is 16 GB of RAM enough headroom for the always-on model, or is it the cause of
  the occasional job stall?

## Log

- 2026-01-08 — Stood up the workflow engine and database in containers.
- 2026-01-12 — Wired the transcription service; voice capture now lands locally.
- 2026-01-15 — Caught a scheduled job failing silently; health check is now top
  of the next-actions list.
